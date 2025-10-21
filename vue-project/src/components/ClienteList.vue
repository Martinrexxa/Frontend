<template>
  <div>
    <h2>📋 Lista de Clientes</h2>

    <!-- Mensaje de carga -->
    <p v-if="loading">Cargando clientes...</p>
    <p v-if="error" style="color:red">{{ error }}</p>

    <table v-if="!loading && clientes.length" border="1" cellpadding="8">
      <thead>
        <tr>
          <th>ID</th>
          <th>Nombre</th>
          <th>Teléfono</th>
          <th>Correo</th>
          <th>Dirección</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="cliente in clientes" :key="cliente.id_cliente">
          <td>{{ cliente.id_cliente }}</td>
          <td>{{ cliente.nombre }}</td>
          <td>{{ cliente.telefono }}</td>
          <td>{{ cliente.correo }}</td>
          <td>{{ cliente.direccion }}</td>
        </tr>
      </tbody>
    </table>

    <p v-else-if="!loading && !clientes.length">No hay clientes registrados.</p>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";

// 🔹 Interfaz de tipo Cliente
interface Cliente {
  id_cliente: number;
  nombre: string;
  telefono: string;
  correo: string;
  direccion: string;
}

// 🔹 Variables reactivas
const clientes = ref<Cliente[]>([]);
const loading = ref(true);
const error = ref("");

// 🔹 URL de tu backend (ajústala si estás en Vercel)
const API_URL = "https://TU_BACKEND.vercel.app/pablo"; 
// Ejemplo local: "http://localhost:3000/pablo"

// 🔹 Cargar clientes al iniciar
onMounted(async () => {
  try {
    const res = await fetch(API_URL);
    if (!res.ok) throw new Error("Error al obtener clientes");
    clientes.value = await res.json();
  } catch (err: any) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
});
</script>

<style scoped>
table {
  border-collapse: collapse;
  width: 80%;
  margin: 20px auto;
}
th {
  background: #0077cc;
  color: white;
}
td,
th {
  padding: 8px;
  text-align: left;
}
</style>
