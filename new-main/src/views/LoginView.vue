<template>
  <div class="login-page">

    <div class="background-circle"></div>
    <div class="background-shape"></div>

    <div class="login-card">

      <h1 class="logo">
        Soport<span>IA</span>
      </h1>

      <p class="subtitle">
        ASISTENCIA TÉCNICA AL INSTANTE
      </p>

      <div class="welcome">

        <div class="robot">
          🤖
        </div>

        <div>
          <h2>¡Hola!</h2>
          <p>¡Estoy aquí para ayudarte!</p>
        </div>

      </div>

      <label>Correo electrónico:</label>

      <input
        v-model="username"
        type="text"
        placeholder="Ingresa tu correo"
      />

      <label>Contraseña:</label>

      <div class="password-container">

        <input
          v-model="password"
          :type="showPassword ? 'text' : 'password'"
          placeholder="Ingresa tu contraseña"
        />

        <span
          class="eye"
          @click="showPassword = !showPassword"
        >
          👁
        </span>

      </div>

      <div class="remember">

        <input type="checkbox" />

        <span>Recordar</span>

      </div>

      <button @click="login">
        Iniciar sesión
      </button>

      <p class="register">
        ¿No tienes cuenta?
      </p>

      <p class="message">
        {{ message }}
      </p>

    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const username = ref('')
const password = ref('')
const message = ref('')
const showPassword = ref(false)

const login = async () => {

  const res = await fetch("http://127.0.0.1:8000/api/login/", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      username: username.value,
      password: password.value
    })
  })

  const data = await res.json()

  if (res.ok) {

    // guardar usuario
    localStorage.setItem("user", JSON.stringify(data))

    message.value = "Bienvenida " + data.username

    // redirigir
    router.push("/dashboard")

  } else {

    message.value = data.error || "Error en login"
  }
}
</script>
<style scoped>
:global(body){
  margin:0;
  padding:0;
}

:global(html){
  margin:0;
  padding:0;
}

:global(#app){
  margin:0;
  padding:0;
}

*{
  margin:0;
  padding:0;
  box-sizing:border-box;
  font-family:Arial, Helvetica, sans-serif;
}

.login-page{
  width:100%vw;
  height:100vh;
  background:#1b0840;
  display:flex;
  justify-content:center;
  align-items:center;
  position:relative;
  overflow:hidden;

}


  .background-circle{
  width:300px;
  height:300px;
  background:#243b92;
  border-radius:50%;
  position:absolute;
  left:25%;
  top:15%;
  opacity:.6;
  animation: floatCircle 6s ease-in-out infinite;
}

.background-shape{
  width:220px;
  height:220px;
  background:#243b92;
  position:absolute;
  bottom:10%;
  left:35%;
  transform:rotate(45deg);
  opacity:.5;
  animation: floatShape 8s ease-in-out infinite;
}

.login-card{
  width:360px;
  background:#1f1247;
  padding:40px;
  border-radius:25px;
  position:relative;
  z-index:2;
  color:white;
  box-shadow:0 0 20px rgba(0,0,0,.3);
}

.logo{
  text-align:center;
  font-size:42px;
  font-weight:bold;
}

.logo span{
  color:#b14cff;
}

.subtitle{
  text-align:center;
  font-size:11px;
  letter-spacing:2px;
  margin-top:5px;
  margin-bottom:30px;
}

.welcome{
  display:flex;
  gap:15px;
  align-items:center;
  margin-bottom:25px;
}

.robot{
  font-size:40px;
}

.welcome h2{
  font-size:28px;
}

.welcome p{
  font-size:14px;
}

label{
  display:block;
  margin-bottom:8px;
  margin-top:15px;
  font-size:14px;
  font-weight:bold;
}

input{
  width:100%;
  padding:12px;
  border:none;
  border-radius:10px;
  color:rgb(7, 6, 6);
  outline:none;
}

.password-container{
  position:relative;
}

.eye{
  position:absolute;
  right:15px;
  top:12px;
  cursor:pointer;
}

.remember{
  display:flex;
  align-items:center;
  gap:8px;
  margin-top:15px;
  font-size:13px;
}

.remember input{
  width:auto;
}

button{
  width:100%;
  margin-top:25px;
  padding:14px;
  border:none;
  border-radius:12px;
  background:linear-gradient(to right,#8e2de2,#c84bff);
  color:white;
  font-size:16px;
  font-weight:bold;
  cursor:pointer;
  transition:.3s;
}

button:hover{
  transform:scale(1.03);
}

.register{
  text-align:center;
  margin-top:20px;
  font-size:14px;
}

.message{
  margin-top:15px;
  text-align:center;
  color:#c8ffb0;
}

@keyframes floatCircle{

  0%{
    transform:translateY(0px);
  }

  50%{
    transform:translateY(-30px);
  }

  100%{
    transform:translateY(0px);
  }

}

@keyframes floatShape{

  0%{
    transform:rotate(45deg) translateY(0px);
  }

  50%{
    transform:rotate(45deg) translateY(25px);
  }

  100%{
    transform:rotate(45deg) translateY(0px);
  }

}

</style>

