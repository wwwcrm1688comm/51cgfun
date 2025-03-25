后端（Node.js + Socket.io）
javascript
// server.js
const express = require('express');
const http = require('http');
const socketIo = require('socket.io');
const { v4: uuidv4 } = require('uuid');
 
const app = express();
const server = http.createServer(app);
const io = socketIo(server);
 
let rooms = {};
 
io.on('connection', (socket) => {
  console.log('a user connected');
 
  socket.on('stream', (stream) => {
    const roomId = uuidv4();
    rooms[roomId] = { socket, stream };
 
    socket.join(roomId);
    socket.emit('roomId', roomId);
 
    io.to(roomId).emit('remoteStream', stream);
  });
 
  socket.on('disconnect', () => {
    console.log('user disconnected');
    const roomId = Object.keys(rooms).find(id => rooms[id].socket.id === socket.id);
    if (roomId) {
      delete rooms[roomId];
    }
  });
});
 
server.listen(3001, () => {
  console.log('listening on *:3001');
});
