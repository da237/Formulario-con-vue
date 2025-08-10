<template>
  <h1>Formulario</h1>

  <form @submit.prevent="addPerson">
    <label for="nombre">Nombre</label>
    <input id="nombre" v-model="persona.nombre" placeholder="Ingrese su nombre" />

    <label for="cedula">Cédula</label>
    <input id="cedula" v-model="persona.cedula" placeholder="Ingrese su cédula" />

    <label for="celular">Celular</label>
    <input id="celular" v-model="persona.celular" placeholder="Ingrese su celular" />

    <label for="email">Correo</label>
    <input id="email" v-model="persona.email" placeholder="Ingrese su correo" />

    <button type="submit">Agregar</button>
  </form>

  <table>
    <thead>
      <tr><th>Nombre</th><th>Cédula</th><th>Celular</th><th>Email</th></tr>
    </thead>
    <tbody>
      <tr v-for="(item, index) in datos" :key="item.id ?? index">
        <td>{{ item.nombre }}</td>
        <td>{{ item.cedula }}</td>
        <td>{{ item.celular }}</td>
        <td>{{ item.email }}</td>
      </tr>
    </tbody>
  </table>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue';

const persona = ref({ nombre: '', cedula: '', celular: '', email: '' });
const datos = ref([]);

onMounted(() => {
  const saved = localStorage.getItem('datos');
  if (saved) {
    try {
      datos.value = JSON.parse(saved);
    } catch (e) {
      console.warn('localStorage datos corruptos, limpiando', e);
      localStorage.removeItem('datos');
      datos.value = [];
    }
  }
});

// guarda automáticamente cuando cambian los datos
watch(datos, (val) => {
  localStorage.setItem('datos', JSON.stringify(val));
}, { deep: true });

const validateEmail = (s) => /\S+@\S+\.\S+/.test(s);

const addPerson = () => {
  // trim para evitar solo espacios
  const n = persona.value.nombre.trim();
  const c = persona.value.cedula.trim();
  const cel = persona.value.celular.trim();
  const e = persona.value.email.trim();

  if (!n || !c || !cel || !e) {
    alert('Todos los campos son obligatorios');
    return;
  }
  if (!validateEmail(e)) {
    alert('El email no tiene un formato válido');
    return;
  }

  const id = (crypto && crypto.randomUUID) ? crypto.randomUUID() : Date.now().toString();
  const newPerson = { id, nombre: n, cedula: c, celular: cel, email: e };

  datos.value.push(newPerson);

  // limpiar
  persona.value = { nombre: '', cedula: '', celular: '', email: '' };
};
</script>
