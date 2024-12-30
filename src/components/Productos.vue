<template>
  <div class="page-container">
    <h2 class="text-center text-dark mb-4">Mis Productos</h2>
      
    <div class="card">
      <div class="card-body">
      <!-- Barra de búsqueda y filtros -->
      <div class="row mb-3">
        <div class="col-md-2"></div>
        <div class="col-md-1 pad-7">Buscar:</div>
        <div class="col-md-2">
          <input 
            type="text" 
            class="form-control" 
            v-model="busqueda" 
            placeholder="Buscar producto por nombre" 
          />
        </div>
        <div class="col-md-2"></div>
        <div class="col-md-1 pad-7">Filtrar:</div>
        <div class="col-md-2">
          <select 
            class="form-control" 
            v-model="filtroPrecio" 
          >
            <option value="">Todos los precios</option>
            <option value="100">Precio mayor a 100</option>
            <option value="200">Precio mayor a 200</option>
          </select>
        </div>
      </div>
      <div class="row">    
      <div class="col-md-10"></div>
        <div class="col-md-2">
          <button class="btn btn-success mb-3 bi bi-cart-plus-fill" @click="abrirModal()">&nbsp;Añadir Producto</button>
        </div>
        </div>   
      <!-- Tabla de productos -->
      <table class="table table-hover">
        <thead>
          <tr>
            <th>ID</th>
            <th>Nombre</th>
            <th>Precio</th>
            <th>Stock</th>
            <th>Categoría</th>
            <th>Acciones</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="producto in productosFiltrados" :key="producto.id">
            <td>{{ producto.id }}</td>
            <td>{{ producto.nombre }}</td>
            <td>{{ producto.precio }}</td>
            <td>{{ producto.stock }}</td>
            <td>{{ getCategoriaNombre(producto.categoriaId) }}</td>
            <td>
              <button @click="abrirModal(producto)" class="btn btn-warning btn-sm bi bi-pencil-square"></button>
              <button @click="eliminarProducto(producto.id)" class="btn btn-danger btn-sm bi bi-trash"></button>
            </td>
          </tr>
        </tbody>
      </table>
  
      <!-- Modal -->
      <div class="modal fade" id="modalProducto" tabindex="-1" aria-hidden="true">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">{{ producto.id ? 'Editar Producto' : 'Añadir Producto' }}</h5>
              <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
              <form @submit.prevent="guardarProducto">
                <div class="mb-3">
                  <label for="nombre" class="form-label">Nombre</label>
                  <input type="text" id="nombre" v-model="producto.nombre" class="form-control" required />
                </div>
                <div class="mb-3">
                  <label for="precio" class="form-label">Precio</label>
                  <input type="number" id="precio" v-model="producto.precio" class="form-control" required />
                </div>
                <div class="mb-3">
                  <label for="stock" class="form-label">Stock</label>
                  <input type="number" id="stock" v-model="producto.stock" class="form-control" required />
                </div>
                <div class="mb-3">
                  <label for="categoria" class="form-label">Categoría</label>
                  <select id="categoria" v-model="producto.categoriaId" class="form-control" required>
                    <option v-for="categoria in categorias" :key="categoria.id" :value="categoria.id">
                      {{ categoria.nombre }}
                    </option>
                  </select>
                </div>
                <button type="submit" class="btn btn-primary">Guardar</button>
              </form>
            </div>
          </div>
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
        productos: [],
        categorias: [],
        producto: { id: null, nombre: '', precio: '', stock: '', categoriaId: null },
        busqueda: '',
        filtroPrecio: '',
      };
    },
    computed: {
      productosFiltrados() {
        // Filtrar productos por nombre y precio
        return this.productos.filter((producto) => {
          const coincideBusqueda = this.busqueda
            ? producto.nombre.toLowerCase().includes(this.busqueda.toLowerCase())
            : true;
          const coincideFiltroPrecio = this.filtroPrecio
            ? producto.precio > Number(this.filtroPrecio)
            : true;
          return coincideBusqueda && coincideFiltroPrecio;
        });
      },
    },
    methods: {
      async cargarDatos() {
        const [productosRes, categoriasRes] = await Promise.all([
          api.get('/productos'),
          api.get('/categorias'),
        ]);
        this.productos = productosRes.data;
        this.categorias = categoriasRes.data;
      },
      abrirModal(producto = { id: null, nombre: '', precio: '', categoriaId: null }) {
        this.producto = { ...producto };        
        const modalElement = document.getElementById('modalProducto'); // Reemplaza con el ID real de tu modal
        const modal = new Modal(modalElement);
        modal.show();
      },
      cerrarModal() {
        const modalElement = document.getElementById('modalProducto'); // Reemplaza con el ID de tu modal
        const modalInstance = Modal.getInstance(modalElement); // Obtén la instancia del modal        
        if (modalInstance) {
            modalInstance.hide(); // Cierra el modal
        }
      },
      async guardarProducto() {
        if (this.producto.id) {
          await api.put(`/productos/${this.producto.id}`, this.producto);
        } else {
          let seq=this.productos.length;          
          this.producto.id=(seq+1).toString();
          await api.post('/productos', this.producto);
        }
        this.cargarDatos();
        this.cerrarModal();
      },
      async eliminarProducto(id) {
        await api.delete(`/productos/${id}`);
        this.cargarDatos();
      },
      getCategoriaNombre(id) {
        const categoria = this.categorias.find((cat) => cat.id === id);
        return categoria ? categoria.nombre : 'Sin Categoría';
      },
    },
    created() {
      this.cargarDatos();
    },
  };
  </script>