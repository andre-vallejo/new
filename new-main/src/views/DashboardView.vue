<template>
  <div class="dashboard">

    <!-- NAVBAR -->
    <header class="navbar">

      <button
        class="menu-btn"
        @click="toggleSidebar"
      >
        ☰
      </button>

      <h1 class="logo">
        CIVIS
      </h1>

    </header>

    <!-- SIDEBAR -->
    <aside
      :class="['sidebar', { active: sidebarOpen }]"
    >

      <!-- INICIO -->
      <router-link
        to="/dashboard"
        class="menu-item"
      >
        Inicio
      </router-link>

      <!-- SOLO ADMIN -->
      <router-link
        v-if="userRole === 'admin'"
        to="/users"
        class="menu-item"
      >
        Usuarios
      </router-link>

      <!-- PERFIL -->
      <router-link
        to="/profile"
        class="menu-item"
      >
        Mi Perfil
      </router-link>

      <!-- LOGOUT -->
      <button
        class="menu-item logout"
        @click="logout"
      >
        Cerrar sesión
      </button>

    </aside>

    <!-- CONTENIDO -->
    <main class="content">

      <h2>
        Bienvenido a CIVIS
      </h2>

      <p>
        Sistema de gestión de usuarios
      </p>

      <!-- CARDS -->
      <div class="cards">

        <!-- USUARIOS -->
        <div
          class="card"
          v-if="userRole === 'admin'"
        >

          <h3>
            Usuarios
          </h3>

          <p>
            Gestiona usuarios del sistema
          </p>

        </div>

        <!-- ROLES -->
        <div
          class="card"
          v-if="userRole === 'admin'"
        >

          <h3>
            Roles
          </h3>

          <p>
            Administra permisos y accesos
          </p>

        </div>

        <!-- PERFIL -->
        <div class="card">

          <h3>
            Perfil
          </h3>

          <p>
            Configura tu información
          </p>

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

/* ROL ACTUAL */
const userRole =
  localStorage.getItem("role")

/* ABRIR SIDEBAR */
const toggleSidebar = () => {

  sidebarOpen.value =
    !sidebarOpen.value

}

/* LOGOUT */
const logout = () => {

  localStorage.removeItem("token")

  localStorage.removeItem("role")

  router.push("/")

}

/* SOLO PARA PRUEBAS */
/*
localStorage.setItem(
  "role",
  "admin"
)
*/

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

  box-shadow:
    0 2px 10px rgba(0,0,0,0.2);

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

  box-shadow:
    2px 0 10px rgba(0,0,0,0.2);

  padding-top: 20px;

  z-index: 100;

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

  transition: 0.2s;

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

.content h2 {

  color: #6a0dad;

}

/* CARDS */

.cards {

  display: grid;

  grid-template-columns:
    repeat(auto-fit, minmax(250px, 1fr));

  gap: 20px;

  margin-top: 20px;

}

.card {

  background: white;

  padding: 20px;

  border-radius: 12px;

  box-shadow:
    0 2px 10px rgba(0,0,0,0.1);

  transition: 0.3s;

}

.card:hover {

  transform: translateY(-5px);

}

.card h3 {

  margin-bottom: 10px;

  color: #6a0dad;

}

.card p {

  color: #666;

}

</style>
```

