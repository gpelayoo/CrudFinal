<template>
    <div class="container">
      <h2>Lista de personas</h2>
      <ul class="list-group">
        <li v-for="persona in personas" :key="persona.id" class="list-group-item">
          {{ persona.nombre }}
          <button class="btn-editar" @click="editarPersona(persona)">Editar</button>
          <button class="btn-eliminar" @click="eliminarPersona(persona)">Eliminar</button>
        </li>
      </ul>
    
      <h2>Agregar Persona</h2>
      <input type="text" v-model="nuevaPersona.nombre">
      <button @click="agregarPersona" :disabled="nuevaPersona.nombre === ''">Agregar</button>
    
      <h2>Editar Persona</h2>
      <input type="text" v-model="nuevaPersona.nombre" v-if="personaEditada" class="input-editar">
      <button @click="guardarEdicion" v-if="personaEditada && nuevaPersona.nombre !== ''" class="btn-guardar">Guardar</button>
      <button @click="cancelarEdicion" v-if="personaEditada" class="btn-cancelar">Cancelar</button>
    </div>
</template>


<script>
import axios from 'axios';

export default {
  data() {
    return {
      personas: [],
      nuevaPersona: { nombre: '', apellido: '' },
      personaEditada: null
    };
  },

  methods: {
    async agregarPersona() {
      if (this.nuevaPersona.nombre !== '' && this.personaEditada === null) {
        try {
          const response = await axios.post('/api/personas', this.nuevaPersona);
          const persona = response.data;
          this.personas.push(persona);
          this.nuevaPersona = { nombre: '', apellido: '' };
        } catch (error) {
          console.error(error);
        }
      }
    },

    editarPersona(persona) {
      this.personaEditada = persona;
      this.nuevaPersona = { nombre: persona.nombre, apellido: persona.apellido };
    },

    async guardarEdicion() {
      const personaIndex = this.personas.findIndex(p => p.id === this.personaEditada.id);

      if (personaIndex !== -1) {
        try {
          const response = await axios.put(`/api/personas/${this.personaEditada.id}`, this.nuevaPersona);
          const persona = response.data;
          this.personas[personaIndex] = persona;
          this.cancelarEdicion();
        } catch (error) {
          console.error(error);
        }
      }
    },

    cancelarEdicion() {
      this.personaEditada = null;
      this.nuevaPersona = { nombre: '', apellido: '' };
    },

    async eliminarPersona(persona) {
      const personaIndex = this.personas.findIndex(p => p.id === persona.id);

      if (personaIndex !== -1) {
        try {
          await axios.delete(`/api/personas/${persona.id}`);
          this.personas.splice(personaIndex, 1);
        } catch (error) {
          console.error(error);
        }
      }
    },

    async obtenerPersonas() {
      try {
        const response = await axios.get('/api/personas');
        this.personas = response.data;
      } catch (error) {
        console.error(error);
      }
    }
  },

  created() {
    this.obtenerPersonas();
  }
};
</script>