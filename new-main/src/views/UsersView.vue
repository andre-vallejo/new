<template>
  <div class="layout">
    <aside class="sidebar">
      <h2 class="logo">CIVIS</h2>

      <router-link to="/dashboard" class="menu-item">
        Inicio
      </router-link>

      <router-link to="/users" class="menu-item active">
        Usuarios
      </router-link>

      <router-link to="/profile" class="menu-item">
        Mi Perfil
      </router-link>
    </aside>

    <main class="content">
      <div class="topbar">
        <h1>Gestión de Usuarios</h1>
      </div>

      <div class="form-card">
        <h2>
          {{ isEditing ? 'Editar Usuario' : 'Crear Usuario' }}
        </h2>
        
        <div class="form-grid">
          <div class="form-group" style="display: flex; flex-direction: column; gap: 5px;">
            <label style="font-weight: 600; text-align: left;">Nombre de usuario</label>
            <input v-model="form.username" type="text" />
          </div>

          <div class="form-group" style="display: flex; flex-direction: column; gap: 5px;">
            <label style="font-weight: 600; text-align: left;">Correo electrónico</label>
            <input v-model="form.email" type="email" />
          </div>

          <div class="form-group" style="display: flex; flex-direction: column; gap: 5px;">
            <label style="font-weight: 600; text-align: left;">Contraseña</label>
            <input v-model="form.password" type="password" />
          </div>

          <div class="form-group" style="display: flex; flex-direction: column; gap: 5px;">
            <label style="font-weight: 600; text-align: left;">Rol asignado</label>
            <select v-model="form.role">
              <option disabled value="">Selecciona rol</option>
              <option v-for="role in roles" :key="role.id" :value="role.id">
                {{ role.name }}
              </option>
            </select>
          </div>

          <div class="form-group" style="display: flex; flex-direction: column; gap: 5px;">
            <label style="font-weight: 600; text-align: left;">Cliente asociado</label>
            <select v-model="form.client">
              <option disabled value="">Selecciona cliente</option>
              <option v-for="client in clients" :key="client.id" :value="client.id">
                {{ client.name }}
              </option>
            </select>
          </div>
        </div>

        <div class="buttons">
          <button class="save-btn" @click="isEditing ? updateUser() : createUser()">
            {{ isEditing ? 'Actualizar' : 'Crear' }}
          </button>

          <button v-if="isEditing" class="cancel-btn" @click="cancelEdit">
            Cancelar
          </button>
        </div>
      </div>

      <div class="table-card">
        <div class="table-header">
          <h2>Usuarios Registrados</h2>
          <button @click="getUsers">Recargar</button>
        </div>

        <table>
          <thead>
            <tr>
              <th>ID</th>
              <th>Usuario</th>
              <th>Email</th>
              <th>Rol</th>
              <th>Cliente</th>
              <th>Estado</th>
              <th>Opciones</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="user in users" :key="user.id">
              <td>{{ user.id }}</td>
              <td>{{ user.username }}</td>
              <td>{{ user.email }}</td>
              <td>{{ user.role_name }}</td>
              <td>{{ user.client_name }}</td>
              <td>
                <span v-if="user.is_active" class="active">Activo</span>
                <span v-else class="inactive">Inactivo</span>
              </td>
              <td class="actions">
                <button class="edit-btn" @click="editUser(user)">Editar</button>
                <button v-if="user.is_active" class="delete-btn" @click="toggleUser(user.id, false)">Desactivar</button>
                <button v-else class="enable-btn" @click="toggleUser(user.id, true)">Reactivar</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  data() {
    return {
      users: [],
      roles: [],   // Vienen de la base de datos de Django
      clients: [], // Vienen de la base de datos de Django
      isEditing: false,
      editingUserId: null,
      form: {
        username: '',
        email: '',
        password: '',
        role: '',
        client: ''
      }
    }
  },
  methods: {
    async getUsers() {
      const res = await fetch("http://127.0.0.1:8000/api/users/")
      this.users = await res.json()
    },

    // Trae los roles de Django. Si no hay ninguno, los crea en la BD automáticamente
    async verifyAndLoadRoles() {
      try {
        let res = await fetch("http://127.0.0.1:8000/api/roles/")
        let data = await res.json()
        
        if (data.length === 0) {
          const rolesA_Inyectar = ['Administrador', 'Soporte', 'Usuario Estándar', 'Cliente']
          for (let name of rolesA_Inyectar) {
            await fetch("http://127.0.0.1:8000/api/roles/", {
              method: "POST",
              headers: { "Content-Type": "application/json" },
              body: JSON.stringify({ name: name })
            })
          }
          res = await fetch("http://127.0.0.1:8000/api/roles/")
          data = await res.json()
        }
        this.roles = data
      } catch (error) {
        console.error("Error conectando con la tabla de roles:", error)
      }
    },

    // Trae los clientes de Django. Si no hay ninguno, crea el principal en la BD
    async verifyAndLoadClients() {
      try {
        let res = await fetch("http://127.0.0.1:8000/api/clients/")
        let data = await res.json()
        
        if (data.length === 0) {
          await fetch("http://127.0.0.1:8000/api/clients/", {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify({ name: 'CIVIS Principal' })
          })
          res = await fetch("http://127.0.0.1:8000/api/clients/")
          data = await res.json()
        }
        this.clients = data
      } catch (error) {
        console.error("Error conectando con la tabla de clientes:", error)
      }
    },

    async createUser() {
      // VALIDACIONES DE CAMPOS OBLIGATORIOS
      if (!this.form.username || !this.form.username.trim()) {
        alert("El campo 'Nombre de usuario' es obligatorio.")
        return
      }
      if (!this.form.email || !this.form.email.trim()) {
        alert("El campo 'Correo electrónico' es obligatorio.")
        return
      }
      if (!this.form.password || !this.form.password.trim()) {
        alert("El campo 'Contraseña' es obligatorio.")
        return
      }
      if (!this.form.role) {
        alert("Por favor, selecciona un Rol asignado.")
        return
      }
      if (!this.form.client) {
        alert("Por favor, selecciona un Cliente asociado.")
        return
      }

      // PETICIÓN POST A DIJANGO SI LAS VALIDACIONES PASAN
      const res = await fetch("http://127.0.0.1:8000/api/users/", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(this.form)
      })
      
      if (res.ok) {
        alert("Usuario creado exitosamente en la base de datos")
        this.getUsers()
        this.resetForm()
      } else {
        const errorData = await res.json()
        alert("Error al guardar en el servidor: " + JSON.stringify(errorData))
      }
    },

    editUser(user) {
      this.isEditing = true
      this.editingUserId = user.id
      this.form.username = user.username
      this.form.email = user.email
      this.form.password = ''
      this.form.role = user.role
      this.form.client = user.client
    },

    async updateUser() {
      // VALIDACIONES PARA LA EDICIÓN (Exceptuando contraseña si se desea mantener la misma)
      if (!this.form.username || !this.form.username.trim()) {
        alert("El campo 'Nombre de usuario' es obligatorio.")
        return
      }
      if (!this.form.email || !this.form.email.trim()) {
        alert("El campo 'Correo electrónico' es obligatorio.")
        return
      }
      if (!this.form.role) {
        alert("Por favor, selecciona un Rol asignado.")
        return
      }
      if (!this.form.client) {
        alert("Por favor, selecciona un Cliente asociado.")
        return
      }

      const payload = {
        username: this.form.username,
        email: this.form.email,
        role: this.form.role,
        client: this.form.client
      }
      if (this.form.password && this.form.password.trim()) {
        payload.password = this.form.password
      }
      
      const res = await fetch(`http://127.0.0.1:8000/api/users/${this.editingUserId}/`, {
        method: "PUT",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(payload)
      })
      if (res.ok) {
        alert("Usuario actualizado con éxito")
        this.getUsers()
        this.resetForm()
      } else {
        const errorData = await res.json()
        alert("Error al actualizar en el servidor: " + JSON.stringify(errorData))
      }
    },

    async toggleUser(id, active) {
      const res = await fetch(`http://127.0.0.1:8000/api/users/${id}/`, {
        method: "PATCH",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ is_active: active })
      })
      if (res.ok) this.getUsers()
    },

    resetForm() {
      this.form = { username: '', email: '', password: '', role: '', client: '' }
      this.isEditing = false
      this.editingUserId = null
    },

    cancelEdit() {
      this.resetForm()
    }
  },
  mounted() {
    this.getUsers()
    this.verifyAndLoadRoles()   // Inyecta roles en la BD de Django si está vacía
    this.verifyAndLoadClients() // Inyecta cliente principal en la BD de Django si está vacía
  }
}
</script>

<style scoped>
.layout {
  display: flex;
  min-height: 100vh;
  background: #f5f5f5;
}

.sidebar {
  width: 250px;
  background: #6a0dad;
  color: white;
  padding: 20px;
}

.logo {
  margin-bottom: 40px;
}

.menu-item {
  display: block;
  color: white;
  text-decoration: none;
  padding: 12px;
  border-radius: 8px;
  margin-bottom: 10px;
}

.menu-item:hover,
.active {
  background: rgba(255,255,255,0.2);
}

.content {
  flex: 1;
  padding: 30px;
}

.topbar h1 {
  color: #6a0dad;
}

.form-card,
.table-card {
  background: white;
  padding: 20px;
  border-radius: 15px;
  margin-top: 20px;
}

.form-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 15px;
  margin-top: 20px;
}

input,
select {
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 8px;
  width: 100%;
  box-sizing: border-box;
}

.buttons {
  margin-top: 20px;
}

.save-btn,
.cancel-btn,
.edit-btn,
.delete-btn,
.enable-btn {
  border: none;
  padding: 10px 15px;
  border-radius: 8px;
  color: white;
  cursor: pointer;
  margin-right: 10px;
}

.save-btn {
  background: #6a0dad;
}

.cancel-btn {
  background: gray;
}

.edit-btn {
  background: #2563eb;
}

.delete-btn {
  background: red;
}

.enable-btn {
  background: green;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

th {
  background: #6a0dad;
  color: white;
  padding: 12px;
}

td {
  padding: 12px;
  border-bottom: 1px solid #ddd;
}

.active {
  color: green;
  font-weight: bold;
}

.inactive {
  color: red;
  font-weight: bold;
}
</style>