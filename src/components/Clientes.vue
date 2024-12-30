<template>
    <div class="page-container">
      <h2 class="text-center text-dark mb-4">Mis Clientes</h2>
      <div class="card">
        <div class="card-body">
      <!-- Barra de búsqueda y filtro -->
      <div class="row mb-3">
        <div class="col-md-2"></div>
        <div class="col-md-1 pad-7">Buscar:</div>
        <div class="col-md-2">
          <input 
            type="text" 
            class="form-control" 
            v-model="busqueda" 
            placeholder="Buscar cliente por nombre" 
          />
        </div>
        <div class="col-md-2"></div>
        <div class="col-md-1 pad-7">Filtrar:</div>
        <div class="col-md-2">
          <select class="form-control" v-model="filtroDominio">
            <option value="">Todos</option>
            <option value="@gmail.com">Gmail</option>
            <option value="@yahoo.com">Yahoo</option>
            <option value="@outlook.com">Outlook</option>
          </select>
        </div>
      </div>
      <div class="row">     
        <div class="col-md-10"></div>
        <div class="col-md-2">
          <button class="btn btn-success mb-3 bi bi-person-plus" @click="abrirModal()">&nbsp;Añadir Cliente</button>
        </div>
        </div>
      <table class="table table-hover">
        <thead>
          <tr>
            <th>ID</th>
            <th>Nombre</th>
            <th>Correo</th>
            <th>Telefono</th>
            <th>Dirección</th>
            <th>Acciones</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="cliente in clientesFiltrados" :key="cliente.id">
            <td>{{ cliente.id }}</td>
            <td>{{ cliente.nombre }}</td>
            <td>{{ cliente.email }}</td>
            <td>{{ cliente.telefono }}</td>
            <td>{{ cliente.direccion }}</td>
            <td>
              <button @click="abrirModal(cliente)" class="btn btn-warning btn-sm bi bi-pencil-square"></button>
              <button @click="eliminarCliente(cliente.id)" class="btn btn-danger btn-sm bi bi-trash"></button>
            </td>
          </tr>
        </tbody>
      </table>
  </div>
      </div>
      <!-- Modal -->
      <div class="modal fade" id="modalCliente" tabindex="-1" aria-hidden="true">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">{{ cliente.id ? 'Editar Cliente' : 'Añadir Cliente' }}</h5>
              <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
              <form @submit.prevent="guardarCliente">
                <div class="mb-3">
                  <label for="nombre" class="form-label">Nombre</label>
                  <input type="text" id="nombre" v-model="cliente.nombre" class="form-control" required />
                </div>
                <div class="mb-3">
                  <label for="email" class="form-label">Correo</label>
                  <input type="email" id="email" v-model="cliente.email" class="form-control" required />
                </div>
                <div class="mb-3">
                  <label for="telefono" class="form-label">Telefono</label>
                  <input type="text" id="telefono" v-model="cliente.telefono" class="form-control" required />
                </div>
                <div class="mb-3">
                  <label for="direccion" class="form-label">Dirección</label>
                  <input type="text" id="direccion" v-model="cliente.direccion" class="form-control" required />
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
        clientes: [],
        cliente: { id: null, nombre: '', email: '', telefono: '', direccion: ''  },
        busqueda: '',
        filtroDominio: '',
      };
    },
    computed: {
      clientesFiltrados() {
        return this.clientes.filter((cliente) => {
          const coincideBusqueda = this.busqueda
            ? cliente.nombre.toLowerCase().includes(this.busqueda.toLowerCase())
            : true;
          const coincideFiltroDominio = this.filtroDominio
            ? cliente.email.includes(this.filtroDominio)
            : true;
          return coincideBusqueda && coincideFiltroDominio;
        });
      },
    },
    methods: {
      async cargarClientes() {
        const response = await api.get('/clientes');
        this.clientes = response.data;
      },
      abrirModal(cliente = { id: null, nombre: '', email: '' }) {
        this.cliente = { ...cliente };
              
        const modalElement = document.getElementById('modalCliente'); // Reemplaza con el ID real de tu modal
        const modal = new Modal(modalElement);
        modal.show();
      },
      cerrarModal() {
        const modalElement = document.getElementById('modalCliente'); // Reemplaza con el ID de tu modal
        const modalInstance = Modal.getInstance(modalElement); // Obtén la instancia del modal        
        if (modalInstance) {
            modalInstance.hide(); // Cierra el modal
        }
      },
      async guardarCliente() {
        if (this.cliente.id) {
          await api.put(`/clientes/${this.cliente.id}`, this.cliente);
        } else {
          let seq=this.clientes.length;
          this.cliente.id=(seq+1).toString();
          await api.post('/clientes', this.cliente);
        }
        this.cargarClientes();
        //bootstrap.Modal.getInstance(document.getElementById('modalCliente')).hide();
        this.cerrarModal();
      },
      async eliminarCliente(id) {
        await api.delete(`/clientes/${id}`);
        this.cargarClientes();
      },
    },
    created() {
      this.cargarClientes();
    },
  };
  </script>