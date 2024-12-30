<template>
    <div class="page-container">
      <h2 class="text-center text-dark mb-4">Mis Categorías</h2>
      <div class="card">
        <div class="card-body">
      <!-- Barra de búsqueda -->
      <div class="row mb-3">
        <div class="col-md-2"></div>
        <div class="col-md-1 pad-7">Buscar:</div>
        <div class="col-md-2">
          <input 
            type="text" 
            class="form-control" 
            v-model="busqueda" 
            placeholder="Buscar categoría por nombre" 
          />
        </div>
      </div>
      <div class="row">    
      <div class="col-md-10"></div>
        <div class="col-md-2">
          <button class="btn btn-success mb-3 bi bi-journal-plus" @click="abrirModal()">&nbsp;Añadir Categoría</button>
        </div>
        </div>
   
      <table class="table table-hover">
        <thead>
          <tr>
            <th>ID</th>
            <th>Nombre</th>
            <th>Descripción</th>
            <th>Acciones</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="categoria in categoriasFiltradas" :key="categoria.id">
            <td>{{ categoria.id }}</td>
            <td>{{ categoria.nombre }}</td>
            <td>{{ categoria.descripcion }}</td>
            <td>
              <button @click="abrirModal(categoria)" class="btn btn-warning btn-sm bi bi-pencil-square"></button>
              <button @click="eliminarCategoria(categoria.id)" class="btn btn-danger btn-sm bi bi-trash"></button>
            </td>
          </tr>
        </tbody>
      </table>
  </div>
  </div>
      <!-- Modal -->
      <div class="modal fade" id="modalCategoria" tabindex="-1" aria-hidden="true">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">{{ categoria.id ? 'Editar Categoría' : 'Añadir Categoría' }}</h5>
              <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
              <form @submit.prevent="guardarCategoria">
                <div class="mb-3">
                  <label for="nombre" class="form-label">Nombre</label>
                  <input type="text" id="nombre" v-model="categoria.nombre" class="form-control" required />
                </div>
                <div class="mb-3">
                  <label for="descripcion" class="form-label">Descripción</label>
                  <input type="text" id="descripcion" v-model="categoria.descripcion" class="form-control" required />
                </div>
                <button type="submit" class="btn btn-primary">Guardar</button>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import api from '../services/api';
  import { Modal } from 'bootstrap';
  
  export default {
    data() {
      return {
        categorias: [],
        categoria: { id: null, nombre: '', descripcion: '' },
        busqueda: '',
      };
    },
    computed: {
      categoriasFiltradas() {
        return this.categorias.filter((categoria) =>
          categoria.nombre.toLowerCase().includes(this.busqueda.toLowerCase())
        );
      },
    },
    methods: {
      async cargarCategorias() {
        const response = await api.get('/categorias');
        this.categorias = response.data;
      },
      abrirModal(categoria = { id: null, nombre: '' }) {
        this.categoria = { ...categoria };        
        const modalElement = document.getElementById('modalCategoria'); // Reemplaza con el ID real de tu modal
        const modal = new Modal(modalElement);
        modal.show();
      },
      cerrarModal() {
        const modalElement = document.getElementById('modalCategoria'); // Reemplaza con el ID de tu modal
        const modalInstance = Modal.getInstance(modalElement); // Obtén la instancia del modal        
        if (modalInstance) {
            modalInstance.hide(); // Cierra el modal
        }
      },
      async guardarCategoria() {
        if (this.categoria.id) {
          await api.put(`/categorias/${this.categoria.id}`, this.categoria);
        } else {
          let seq=this.categorias.length;
          this.categoria.id=(seq+1).toString();
          await api.post('/categorias', this.categoria);
        }
        this.cargarCategorias();
        this.cerrarModal();
      },
      async eliminarCategoria(id) {
        await api.delete(`/categorias/${id}`);
        this.cargarCategorias();
      },
    },
    created() {
      this.cargarCategorias();
    },
  };
  </script>