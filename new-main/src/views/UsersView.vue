<template>
  <div class="users-container">

    <!-- HEADER -->
    <div class="header">
      <h1>Gestión de Usuarios</h1>
    </div>

    <!-- FORMULARIO -->
    <div class="form-card">

      <h2>{{ isEditing ? 'Editar Usuario' : 'Crear Usuario' }}</h2>

      <input
        v-model="form.username"
        type="text"
        placeholder="Usuario"
      />

      <input
        v-model="form.email"
        type="email"
        placeholder="Correo"
      />

      <input
        v-model="form.password"
        type="password"
        placeholder="Contraseña"
      />

      <button @click="isEditing ? updateUser() : createUser()">
        {{ isEditing ? 'Actualizar Usuario' : 'Crear Usuario' }}
      </button>

      <button v-if="isEditing" @click="cancelEdit">
        Cancelar
      </button>

    </div>

    <!-- TABLA -->
    <div class="table-card">

      <div class="table-header">
        <h2>Usuarios Registrados</h2>

        <button @click="getUsers">
          Recargar
        </button>
      </div>

      <table>

        <thead>
          <tr>
            <th>ID</th>
            <th>Usuario</th>
            <th>Email</th>
            <th>Acciones</th>
          </tr>
        </thead>

        <tbody>

          <tr v-for="user in users" :key="user.id">

            <td>{{ user.id }}</td>
            <td>{{ user.username }}</td>
            <td>{{ user.email }}</td>

            <td>

              <button class="edit-btn" @click="editUser(user)">
                Editar
              </button>

              <button class="delete-btn" @click="deleteUser(user.id)">
                Eliminar
              </button>

            </td>

          </tr>

        </tbody>

      </table>

    </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      users: [],

      isEditing: false,
      editingUserId: null,

      form: {
        username: '',
        email: '',
        password: ''
      }
    }
  },

  methods: {

    async getUsers() {
      const res = await fetch("http://127.0.0.1:8000/api/users/")
      const data = await res.json()
      this.users = data
    },

    async createUser() {

      const res = await fetch("http://127.0.0.1:8000/api/users/", {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify(this.form)
      })

      if (res.ok) {
        alert("Usuario creado")
        this.getUsers()
        this.resetForm()
      } else {
        alert("Error al crear usuario")
      }
    },

    editUser(user) {

      this.isEditing = true
      this.editingUserId = user.id

      this.form.username = user.username
      this.form.email = user.email
      this.form.password = ''
    },

    async updateUser() {

      const payload = {
        username: this.form.username,
        email: this.form.email
      }

      if (this.form.password) {
        payload.password = this.form.password
      }

      const res = await fetch(
        `http://127.0.0.1:8000/api/users/${this.editingUserId}/`,
        {
          method: "PUT",
          headers: {
            "Content-Type": "application/json"
          },
          body: JSON.stringify(payload)
        }
      )

      if (res.ok) {
        alert("Usuario actualizado")
        this.getUsers()
        this.resetForm()
      } else {
        alert("Error al actualizar usuario")
      }
    },

    async deleteUser(id) {

      if (!confirm("¿Seguro que quieres eliminar este usuario?")) return

      const res = await fetch(
        `http://127.0.0.1:8000/api/users/${id}/`,
        {
          method: "DELETE"
        }
      )

      if (res.ok) {
        alert("Usuario eliminado")
        this.getUsers()
      } else {
        alert("Error al eliminar usuario")
      }
    },

    cancelEdit() {
      this.resetForm()
    },

    resetForm() {
      this.form.username = ''
      this.form.email = ''
      this.form.password = ''

      this.isEditing = false
      this.editingUserId = null
    }

  },

  mounted() {
    this.getUsers()
  }
}
</script>

<style scoped>
.users-container {
  padding: 30px;
  background: #f4f4f4;
  min-height: 100vh;
}

.header h1 {
  color: #6a0dad;
  margin-bottom: 20px;
}

.form-card {
  background: white;
  padding: 20px;
  margin-bottom: 20px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  border-radius: 10px;
}

.form-card input {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 6px;
}

.form-card button {
  background: #6a0dad;
  color: white;
  padding: 10px;
  border: none;
  cursor: pointer;
  border-radius: 6px;
}

.table-card {
  background: white;
  padding: 20px;
  border-radius: 10px;
}

.table-header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
}

.table-header button {
  background: #6a0dad;
  color: white;
  border: none;
  padding: 8px 12px;
  cursor: pointer;
  border-radius: 6px;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th {
  background: #6a0dad;
  color: white;
  padding: 10px;
  text-align: left;
}

td {
  padding: 10px;
  border-bottom: 1px solid #ddd;
}

.edit-btn {
  background: #2563eb;
  color: white;
  border: none;
  padding: 6px 10px;
  margin-right: 5px;
  cursor: pointer;
  border-radius: 5px;
}

.delete-btn {
  background: red;
  color: white;
  border: none;
  padding: 6px 10px;
  cursor: pointer;
  border-radius: 5px;
}
</style>