<template>
  <div>
    <h1 class="text-center">Lista de Personas</h1>
    <hr>

    <button @click="modificar=false; abrirModal();" type="button" class="btn btn-primary my-4">
      Nueva 
    </button>

    <div class="modal" :class="{mostrar:modal}">
      <div class="modal-dialog">
        <div class="modal-content">


          <div class="modal-header">
            <h4 class="modal-title">{{ tituloModal }}</h4>
            <button @click="cerrarModal();" type="button" class="close" data-dismiss="modal">&times;</button>
          </div>

          <div class="modal-body">
            <div class="my-4">
              <label for="nombre">Nombre</label>
              <input v-model="persona.nombre" type="text" class="form-control" id="nombre" placeholder="Nombre">
            </div>

            <div class="my-4">
              <label for="apellido">Apellido</label>
              <input v-model="persona.apellido" type="text" class="form-control" id="apellido" placeholder="Apellido">
            </div>
           
          </div>

          <div class="modal-footer">
            <button  @click="cerrarModal();" type="button" class="btn btn-secondary" data-dismiss="modal">Cancelar</button>
            <button  @click="guardar();" type="button" class="btn btn-success" data-dismiss="modal">Guardar</button>
          </div>
        </div> 
      </div>

    </div>

    <table class="table">
      <thead class="table-dark">
        <tr>
          <th scope="col">#</th>
          <th scope="col">Nombre</th>
          <th scope="col">Apellido</th>
          <th scope="col" colspan="2" class="text-center">Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="persona in personas" :key="persona.id">
          <th scope="row">{{ persona.id }}</th>
          <td>{{ persona.nombre }}</td>
          <td>{{ persona.apellido }}</td>
          <td>
            <button @click="modificar=true; abrirModal(persona);" class="btn btn-warning">Editar</button>
          </td>
          <td>
            <button @click="eliminar(persona.id)" class="btn btn-danger">Eliminar</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>

import axios from 'axios';

export default {

  data(){
    return{
      persona:{
        nombre:'',
        apellido:'',
      },

      id:0,
      modificar:true,
      modal:0,
      tituloModal:'',
      personas:[],
    }
  },

  methods: {
    
    async mostrar(){
      try{
        const $response=await axios.get('/personas');
        this.personas=$response.data;
      }catch(error){
        console.error(error)
      }
      
    },

    async eliminar(id){
      try{
        const $response=await axios.delete('/personas/'+id);
        this.personas=$response.data;
      }catch(error){
        console.error(error)
      }
      this.mostrar();
      
    },

    async guardar(){
      try{

        if(this.modificar){
          const $response = await axios.put('/personas/'+this.id, this.persona);

        }else{
          const $response = await axios.post('/personas', this.persona);
        }
        
      }catch(error){
        console.error(error)
      }
      this.mostrar();
      this.cerrarModal();
      
    },

    abrirModal(data={}){
      this.modal=1
      if(this.modificar){
        this.id=data.id,
        this.tituloModal="Modificar persona";
        this.persona.nombre=data.nombre;
        this.persona.apellido=data.apellido;
      }else{
        this.id=data.id,
        this.tituloModal="Crear persona"
        this.persona.nombre='';
        this.persona.apellido='';
        this.modificar=false;
      }
    },

    cerrarModal(){
      this.modal=0
    },
  },

  created(){
    this.mostrar();
  }
}
</script>

<style>
  .mostrar{
    display: list-item;
    opacity: 1;
    background: rgba(44, 38, 75, 0.849);
  }
</style>