// Simple chat server using Express + Socket.io
const express = require('express');
const http = require('http');
const path = require('path');
const { Server } = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = new Server(server);

// In-memory room store: { roomName: { users: Map(socketId -> username), history: Array<message> } }
const rooms = new Map();

const PORT = process.env.PORT || 3000;

app.use(express.static(path.join(__dirname, 'public')));

io.on('connection', (socket) => {
  console.log('socket connected:', socket.id);

  socket.on('join', ({ username, room }) => {
    if (!username || !room) return;
    username = String(username).trim().slice(0, 32);
    room = String(room).trim().toLowerCase().slice(0, 64);

    socket.data.username = username;
    socket.data.room = room;

    if (!rooms.has(room)) {
      rooms.set(room, { users: new Map(), history: [] });
    }
    const roomData = rooms.get(room);
    roomData.users.set(socket.id, username);
    socket.join(room);

    // Send existing history (last 200 messages)
    socket.emit('history', roomData.history);

    // Notify room of new user list and system message
    io.to(room).emit('users', Array.from(roomData.users.values()));
    const joinMsg = {
      id: generateId(),
      username: 'System',
      text: `${username} joined the room.`,
      ts: Date.now(),
      system: true
    };
    pushHistory(roomData, joinMsg);
    io.to(room).emit('message', joinMsg);

    console.log(`${username} joined room ${room}`);
  });

  socket.on('message', (text) => {
    const username = socket.data.username;
    const room = socket.data.room;
    if (!username || !room) return;
    const cleanText = String(text).slice(0, 2000);
    const msg = {
      id: generateId(),
      username,
      text: cleanText,
      ts: Date.now(),
      system: false
    };
    const roomData = rooms.get(room);
    if (!roomData) return;
    pushHistory(roomData, msg);
    io.to(room).emit('message', msg);
  });

  socket.on('disconnect', () => {
    const username = socket.data.username;
    const room = socket.data.room;
    if (room && rooms.has(room)) {
      const roomData = rooms.get(room);
      roomData.users.delete(socket.id);
      // Emit updated users
      io.to(room).emit('users', Array.from(roomData.users.values()));
      const leaveMsg = {
        id: generateId(),
        username: 'System',
        text: `${username || 'A user'} left the room.`,
        ts: Date.now(),
        system: true
      };
      pushHistory(roomData, leaveMsg);
      io.to(room).emit('message', leaveMsg);

      // If room is empty, remove it to free memory
      if (roomData.users.size === 0) {
        rooms.delete(room);
      }
    }
    console.log('socket disconnected:', socket.id);
  });
});

function pushHistory(roomData, msg) {
  roomData.history.push(msg);
  if (roomData.history.length > 200) {
    roomData.history.shift();
  }
}

function generateId() {
  return Math.random().toString(36).slice(2, 10);
}

server.listen(PORT, () => {
  console.log(`Server listening on http://localhost:${PORT}`);
});
