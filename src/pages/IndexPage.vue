<template>
  <q-page padding>
    <q-btn @click="access">Ingresar</q-btn>
    <q-btn @click="createLink">Crear Link</q-btn>
    {{token}} - {{expiresIn}}
  </q-page>
</template>

<script setup>
  //import axios from 'axios';

  import { api } from 'src/boot/axios';
  import { ref } from 'vue';

  const token = ref("")
  const expiresIn = ref("")

  /*  CON THEN Y CATCH
  const access = () => {
      console.log("me diste click")
      axios
        .post("http://localhost:5000/api/v1/auth/login", {
          email: "rigo@test.com",
          password: "123123"
        })
        .then((res) => {
          console.log(res.data)
        })
        .catch((e) => console.log(e))
    }
  */

  // CON TRY CATCH
  const access = async() => {
    try {
      console.log("me diste click")
      token.value = "tokenjaja"
      expiresIn.value = "expirejaja"     
      const res = await api.post("http://localhost:5000/api/v1/auth/login", {
        email: "rigo@test.com",
        password: "123123"
      })
      // console.log(res.data)
      token.value = res.data.token
      expiresIn.value = res.data.expiresIn
      setTime()
    } catch (error) {
      console.log(error)
    }
  }

  const createLink = async() => {
    try {
      const res = await api({
        method: "POST",
        url: '/links',
        headers: {
          'Authorization': 'Bearer ' + token.value
        },
        data: {
          longLink: 'https://axios-http.com/docs/req_config'
        }
      })
      console.log(res.data)
      
    } catch (error) {
      console.log(error)
    }
  }

  const setTime = () => {
    setTimeout( () => {
      console.log("se refrescó...")
      refreshToken()
    }, expiresIn.value *1000 -6000)
  }

  const refreshToken = async () => {
    try {
      const res = await api.get("/auth/refresh")
      // console.log(res.data)
      token.value = res.data.token
      expiresIn.value = res.data.expiresIn     
      setTime() 
    } catch (error) {
      console.log(error)
    }
  }
  refreshToken()
  
</script>
