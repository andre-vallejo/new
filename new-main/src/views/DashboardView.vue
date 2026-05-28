<template>
  <div class="dashboard">

    <!-- NAVBAR -->
    <header class="navbar">

      <button class="menu-btn" @click="toggleSidebar">
        ☰
      </button>

      <h1 class="logo">CIVIS</h1>

    </header>

    <!-- SIDEBAR -->
    <aside :class="['sidebar', { active: sidebarOpen }]">

      <router-link to="/dashboard" class="menu-item">
        Inicio
      </router-link>

      <router-link to="/users" class="menu-item">
        Usuarios
      </router-link>

      <router-link to="/profile" class="menu-item">
        Mi Perfil
      </router-link>

      <button class="menu-item logout" @click="logout">
        Cerrar sesión
      </button>

    </aside>

    <!-- CONTENIDO -->
    <main class="content">

      <h2>Bienvenido a CIVIS</h2>

      <p>Sistema de gestión de usuarios</p>

      <div class="cards">

        <div class="card">
          <h3>Usuarios</h3>
          <p>Gestiona usuarios del sistema</p>
        </div>

        <div class="card">
          <h3>Roles</h3>
          <p>Administra permisos y accesos</p>
        </div>

        <div class="card">
          <h3>Perfil</h3>
          <p>Configura tu información</p>
        </div>

      </div>

    </main>

  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const sidebarOpen = ref(false)

const toggleSidebar = () => {
  sidebarOpen.value = !sidebarOpen.value
}

const logout = () => {
  localStorage.removeItem("token")
  router.push("/")
}
</script>

<style scoped>

.dashboard {
  min-height: 100vh;
  background: #f5f5f5;
}

/* NAVBAR */
.navbar {
  height: 70px;
  background: #6a0dad;
  display: flex;
  align-items: center;
  padding: 0 20px;
  color: white;
  box-shadow: 0 2px 10px rgba(0,0,0,0.2);
}

.logo {
  font-size: 22px;
}

.menu-btn {
  background: none;
  border: none;
  color: white;
  font-size: 28px;
  margin-right: 20px;
  cursor: pointer;
}

/* SIDEBAR */
.sidebar {
  position: fixed;
  top: 70px;
  left: -260px;
  width: 260px;
  height: 100%;
  background: white;
  transition: 0.3s;
  box-shadow: 2px 0 10px rgba(0,0,0,0.2);
  padding-top: 20px;
}

.sidebar.active {
  left: 0;
}

.menu-item {
  display: block;
  padding: 15px 20px;
  text-decoration: none;
  color: #333;
  font-weight: bold;
  cursor: pointer;
}

.menu-item:hover {
  background: #eee;
}

.logout {
  color: red;
  border: none;
  background: none;
  text-align: left;
  width: 100%;
}

/* CONTENT */
.content {
  padding: 30px;
}

.cards {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
  margin-top: 20px;
}

.card {
  background: white;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

.card h3 {
  margin-bottom: 10px;
  color: #6a0dad;
}

</style>