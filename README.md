const crypto = require('crypto');
const socket = require('socket.io-client');

const socketServerUrl = 'http://localhost:3000'; // Replace with your server URL

const data = require('./data.json'); // Your data source

const generateSecretKey = (message) => {
  const hash = crypto.createHash('sha256');
  hash.update(JSON.stringify(message));
  return hash.digest('hex');
};

const generateRandomMessage = () => {
  const randomData = data[Math.floor(Math.random() * data.length)];
  const secretKey = generateSecretKey(randomData);

  return {
    ...randomData,
    secret_key: secretKey,
  };
};

const generateDataStream = () => {
  const messages = [];
  const streamSize = Math.floor(Math.random() * (499 - 49) + 49);

  for (let i = 0; i < streamSize; i++) {
    const message = generateRandomMessage();
    messages.push(message);
  }

  return messages;
};

const emitterSocket = socket(socketServerUrl);

setInterval(() => {
  const dataStream = generateDataStream();
  emitterSocket.emit('data-stream', dataStream);
}, 10000); //
