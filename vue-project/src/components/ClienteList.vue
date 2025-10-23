<template>
  <div class="container">
    <h1>📋 Clientes</h1>

    <!-- FORMULARIO -->
    <form @submit.prevent="registrarCliente">
      <div class="input-group">
        <label>Nombre:</label>
        <input v-model="cliente.nombre" type="text" required />
      </div>

      <div class="input-group">
        <label>Apellido:</label>
        <input v-model="cliente.apellido" type="text" required />
      </div>

      <div class="input-group">
        <label>Email:</label>
        <input v-model="cliente.email" type="email" required />
      </div>

      <div class="input-group">
        <label>Teléfono:</label>
        <input v-model="cliente.telefono" type="text" />
      </div>

      <button type="submit">Registrar Cliente</button>
    </form>

    <hr />

    <!-- LISTA DE CLIENTES -->
    <h2>Clientes Registrados</h2>
    <p v-if="loading">Cargando clientes...</p>
    <p v-if="error" style="color:red">{{ error }}</p>

    <table v-if="!loading && clientes.length" border="1" cellpadding="8">
      <thead>
        <tr>
          <th>ID</th>
          <th>Nombre</th>
          <th>Apellido</th>
          <th>Email</th>
          <th>Teléfono</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="c in clientes" :key="c.id_cliente">
          <td>{{ c.id_cliente }}</td>
          <td>{{ c.nombre }}</td>
          <td>{{ c.apellido }}</td>
          <td>{{ c.email }}</td>
          <td>{{ c.telefono }}</td>
        </tr>
      </tbody>
    </table>

    <p v-else-if="!loading && !clientes.length">No hay clientes registrados.</p>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";

// ⚡ Definición de tipo para clientes
interface Cliente {
  id_cliente: number;
  nombre: string;
  apellido: string;
  email: string;
  telefono?: string;
}

// Variables reactivas
const clientes = ref<Cliente[]>([]);
const cliente = ref<Omit<Cliente, "id_cliente">>({
  nombre: "",
  apellido: "",
  email: "",
  telefono: ""
});
const loading = ref(true);
const error = ref("");

// 🔗 URL del backend desplegado en Vercel
const API_URL = "https://backend-6eed.onrender.com/api/clientes";


// Función para obtener clientes
const obtenerClientes = async (): Promise<void> => {
  loading.value = true;
  error.value = "";
  try {
    const res = await fetch(API_URL);
    if (!res.ok) throw new Error("Error al obtener clientes");
    const data: Cliente[] = await res.json();
    clientes.value = data;
  } catch (err: unknown) {
    if (err instanceof Error) error.value = err.message;
    else error.value = String(err);
  } finally {
    loading.value = false;
  }
};

// Función para registrar cliente
const registrarCliente = async (): Promise<void> => {
  try {
    const res = await fetch(API_URL, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(cliente.value)
    });
    if (!res.ok) throw new Error("Error al registrar cliente");
    alert("✅ Cliente registrado con éxito");
    cliente.value = { nombre: "", apellido: "", email: "", telefono: "" };
    await obtenerClientes(); // refresca la tabla
  } catch (err: unknown) {
    if (err instanceof Error) alert(err.message);
    else alert(String(err));
  }
};

// Cargar clientes al montar el componente
onMounted(() => {
  obtenerClientes();
});
</script>

<style scoped>
.container {
  max-width: 700px;
  margin: auto;
  padding: 2rem;
  font-family: sans-serif;
  background: #f9f9f9;
  border-radius: 12px;
  box-shadow: 0 0 8px #ccc;
}
.input-group {
  margin-bottom: 1rem;
}
label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}
input {
  width: 100%;
  padding: 8px;
  border-radius: 8px;
  border: 1px solid #ccc;
}
button {
  display: block;
  width: 100%;
  padding: 10px;
  background: #007bff;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  margin-top: 10px;
}
button:hover {
  background: #0056b3;
}
table {
  border-collapse: collapse;
  width: 100%;
  margin-top: 20px;
}
th {
  background: #0077cc;
  color: white;
}
td, th {
  padding: 8px;
  text-align: left;
}
</style>
