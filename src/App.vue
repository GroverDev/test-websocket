<template>
  <h1>WebSocket Test (Socket.IO Client)</h1>
  <div id="output">
    <p v-for="(message, index) in messages" :key="index">{{ message }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { io } from 'socket.io-client';

const messages = ref<string[]>([]);
const socket = ref<ReturnType<typeof io> | null>(null);
const wsUrl = 'wss://ticket.csbp.com.bo/?EIO=4&transport=websocket'; // Adjust if your Socket.IO server has a specific path

onMounted(() => {
  connectSocketIO();
});

function connectSocketIO() {
  messages.value.push('Intentando conectar con Socket.IO...');

  socket.value = io(wsUrl, {
    transports: ['websocket'], // Optionally force WebSocket transport
    autoConnect: true,
  });

  socket.value.on('connect', () => {
    messages.value.push('Conexión Socket.IO establecida.');
    socket.value?.emit('clientToServer', 'Hola desde el cliente Vue!');
  });

  socket.value.on('message', (data: any) => {
    messages.value.push(`Mensaje del servidor: ${data}`);
  });

  socket.value.on('disconnect', () => {
    messages.value.push('Conexión Socket.IO desconectada.');
    // Optionally attempt reconnection
    setTimeout(connectSocketIO, 3000);
  });

  socket.value.on('connect_error', (error: Error) => {
    messages.value.push(`Error de conexión Socket.IO: ${error.message}`);
  });
}
</script>

<style scoped>
#output {
  margin-top: 20px;
  border: 1px solid #ccc;
  padding: 10px;
  white-space: pre-wrap;
}
</style>