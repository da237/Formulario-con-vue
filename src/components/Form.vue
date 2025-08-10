<template>
    <v-container>
        <v-card class="pa-6" max-width="600" elevation="4">
            <v-card-title class="text-h5">Formulario</v-card-title>
            <v-card-text>
                <!-- Campo Nombre -->
                <v-text-field label="Nombre" v-model="persona.nombre" :rules="[v => !!v || 'El nombre es obligatorio']"
                    clearable />

                <!-- Campo Cédula -->
                <v-text-field label="Cédula" v-model="persona.cedula" :rules="[v => !!v || 'La cédula es obligatoria']"
                    clearable />

                <!-- Campo Celular -->
                <v-text-field label="Celular" type="number" v-model="persona.celular"
                    :rules="[v => !!v || 'El celular es obligatorio']" clearable />

                <!-- Campo Email -->
                <v-text-field label="Correo" v-model="persona.email"
                    :rules="[v => /.+@.+\..+/.test(v) || 'Correo inválido']" clearable />

                <!-- Botón -->
                <v-btn color="primary" class="mt-4" @click="addPerson">
                    Agregar
                </v-btn>
            </v-card-text>
        </v-card>

        <!-- Tabla de datos -->
        <v-card class="mt-6" elevation="4">
            <v-card-title class="text-h6">Lista de Personas</v-card-title>
            <v-table>
                <thead>
                    <tr>
                        <th>Nombre</th>
                        <th>Cédula</th>
                        <th>Celular</th>
                        <th>Email</th>
                        <th>Acciones</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(item, index) in datos" :key="index">
                        <td>{{ item.nombre }}</td>
                        <td>{{ item.cedula }}</td>
                        <td>{{ item.celular }}</td>
                        <td>{{ item.email }}</td>
                        <td>
                            <v-btn icon color="red" @click="deletePerson(index)">
                                <v-icon>mdi-delete</v-icon>
                            </v-btn>
                        </td>
                    </tr>
                </tbody>
            </v-table>
        </v-card>

        <!-- Snackbar -->
        <v-snackbar v-model="snackbar.show" :timeout="2000" color="success">
            {{ snackbar.text }}
        </v-snackbar>
    </v-container>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'

// Datos del formulario
const persona = ref({
    nombre: '',
    cedula: '',
    celular: '',
    email: ''
})

// Lista de personas
const datos = ref([])

// Estado del snackbar
const snackbar = ref({
    show: false,
    text: ''
})

// Cargar datos desde localStorage
onMounted(() => {
    const saved = localStorage.getItem('datos')
    if (saved) {
        datos.value = JSON.parse(saved)
    }
})

// Guardar automáticamente cuando cambien los datos
watch(datos, (val) => {
    localStorage.setItem('datos', JSON.stringify(val))
}, { deep: true })

// Agregar persona
const addPerson = () => {
    if (!persona.value.nombre || !persona.value.cedula || !persona.value.celular || !persona.value.email) {
        snackbar.value.text = 'Todos los campos son obligatorios'
        snackbar.value.show = true
        return
    }
    datos.value.push({ ...persona.value })
    persona.value = { nombre: '', cedula: '', celular: '', email: '' }
    snackbar.value.text = 'Datos agregados con éxito'
    snackbar.value.show = true
}

const deletePerson = (index) => {
    datos.value.splice(index, 1); // Elimina el elemento del array
    localStorage.setItem('datos', JSON.stringify(datos.value)); // Guarda el array actualizado
    snackbar.value = {
        text: 'Datos eliminados con éxito',
        show: true
    };
};
</script>

<style scoped>
.v-card {
    border-radius: 12px;
}
</style>
