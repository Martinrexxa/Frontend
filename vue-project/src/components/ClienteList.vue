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
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap');

/* ===== CONTENEDOR PRINCIPAL ===== */
.clientes-container {
  min-height: 100vh;
  background: linear-gradient(135deg, #74ebd5, #9face6);
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 2.5rem 1rem;
  font-family: 'Poppins', sans-serif;
}

/* ===== TARJETAS (FORMULARIO Y TABLA) ===== */
.form-card,
.tabla-card {
  width: 100%;
  max-width: 850px;
  background: rgba(255, 255, 255, 0.18);
  border-radius: 20px;
  backdrop-filter: blur(14px);
  border: 1px solid rgba(255, 255, 255, 0.25);
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.15);
  margin: 1.5rem 0;
  padding: 2.5rem;
  color: #fff;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.form-card:hover,
.tabla-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.25);
}

/* ===== TÍTULOS ===== */
.title {
  text-align: center;
  color: #ffffff;
  text-shadow: 2px 2px 6px rgba(0, 0, 0, 0.3);
  margin-bottom: 2rem;
  font-weight: 700;
  font-size: 1.8rem;
  letter-spacing: 0.5px;
}

/* ===== FORMULARIO ===== */
.formulario {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.5rem;
}

.input-group {
  display: flex;
  flex-direction: column;
}

.input-group label {
  font-weight: 600;
  margin-bottom: 0.4rem;
  font-size: 0.95rem;
  color: #ffffff;
}

.input-group input {
  padding: 10px 15px;
  border-radius: 10px;
  border: none;
  background: rgba(255, 255, 255, 0.95);
  font-size: 1rem;
  color: #333;
  transition: all 0.3s ease;
  box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.1);
}

.input-group input::placeholder {
  color: #aaa;
}

.input-group input:focus {
  background: #ffffff;
  box-shadow: 0 0 6px #74ebd5, 0 0 6px #9face6;
  outline: none;
}

/* ===== BOTÓN ===== */
.btn-registrar {
  grid-column: 1 / 3;
  background: linear-gradient(90deg, #9face6, #74ebd5);
  color: white;
  border: none;
  border-radius: 10px;
  padding: 14px;
  font-size: 1.1rem;
  cursor: pointer;
  font-weight: 700;
  letter-spacing: 0.05em;
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.25);
  transition: all 0.3s ease;
}

.btn-registrar:hover {
  background: linear-gradient(90deg, #74ebd5, #9face6);
  transform: scale(1.03);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.3);
}

/* ===== TABLA ===== */
.table-scroll {
  overflow-x: auto;
  max-width: 100%;
}

table {
  width: 100%;
  min-width: 600px;
  border-collapse: separate;
  border-spacing: 0;
  border-radius: 10px;
  overflow: hidden;
  margin-top: 1.5rem;
  box-shadow: 0 3px 15px rgba(0, 0, 0, 0.2);
  background: rgba(255, 255, 255, 0.85);
  color: #333;
}

th {
  background: linear-gradient(135deg, #74ebd5, #9face6);
  color: white;
  text-transform: uppercase;
  font-weight: 600;
  font-size: 0.9rem;
}

td,
th {
  padding: 14px 15px;
  text-align: left;
}

tbody tr:nth-child(even) {
  background: rgba(255, 255, 255, 0.6);
}

tbody tr:hover {
  background: rgba(255, 255, 255, 0.95);
  transition: 0.3s;
}

/* ===== MENSAJES ===== */
.error {
  color: #ff4d4d;
  text-align: center;
  font-weight: bold;
  background: rgba(255, 0, 0, 0.1);
  padding: 10px;
  border-radius: 8px;
}

.info {
  text-align: center;
  color: #f0f0f0;
  font-style: italic;
  font-weight: 300;
}

/* ===== RESPONSIVIDAD ===== */
@media (max-width: 768px) {
  .formulario {
    grid-template-columns: 1fr;
  }
  .btn-registrar {
    grid-column: 1 / 2;
  }
}
</style>
