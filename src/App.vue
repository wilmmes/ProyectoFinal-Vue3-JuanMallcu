<template>
  <div>
    <!-- Navbar con icono -->
    <nav class="navbar navbar-light navbar-expand-lg mb-3" style="background-color: darkcyan;">
      <div class="container-fluid">
        <a class="navbar-brand" href="#">
          <i class="bi bi-cart4"></i> Fashion Store
        </a>
        <div class="collapse navbar-collapse">
          <ul class="navbar-nav me-auto mb-2 mb-lg-0">
            <li class="nav-item">
              <router-link to="/clientes" class="nav-link">Clientes</router-link>
            </li>
            <li class="nav-item">
              <router-link to="/productos" class="nav-link">Productos</router-link>
            </li>
            <li class="nav-item">
              <router-link to="/categorias" class="nav-link">Categorías</router-link>
            </li>          
          </ul>
        </div>
        <form class="d-flex">
        <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
        <button class="btn btn-success" type="submit">Search</button>
      </form>
      </div>
    </nav>  
    <router-view />
  </div>
</template>

<script>
export default {
  name: 'App',
};
</script>

<style>
.navbar-brand {
  font-weight: bold;
  font-size: 1.5rem;
  display: flex;
  align-items: center;
}
.navbar-brand i {
  margin-right: 0.5rem;
  font-size: 1.8rem;
  color:  white
}
.table th {
    background-color: darkcyan !important;
    color: white;
    text-align: center;
}
</style>