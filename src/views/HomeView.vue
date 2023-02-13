<template>
  <div class="home">
    <div class="left_block" >
      <img class="image" src="../assets/just_do_it.png">
      <img class="image_logo" src="../assets/logoLarge.png">
    </div>

    <div  class="rigth" :style="getLocalStorage()._id !== undefined ? 'width: 10px' : ''" >
      <img class="logo_second" src="../assets/logo_second.png" alt="">
      
      <div v-if="getLocalStorage()._id === undefined" class="tabs">
        <div @click="setRegType(1)" class="tab">Зарегистрироваться</div>
        <div @click="setRegType(2)" class="tab">Войти</div>
      </div>

      <div v-if="getLocalStorage()._id === undefined && regType === 1" class="form">
        <input class="form_item" v-model="form.name" type="text" placeholder="Ваше имя">
        <input class="form_item" v-model="form.email" type="text" placeholder="Ваш электронный адресс">
        <input class="form_item" v-model="form.password" type="password" placeholder="Ваш пароль">
        <input class="form_item" v-model="form.password_again" type="password" placeholder="Повторите пароль">

        <button class="form_item btn" @click="registation()" >Зарегистрироваться</button>
      </div>

      <div v-if="getLocalStorage()._id === undefined && regType === 2" class="form">
        <input class="form_item" v-model="form.email" type="text" placeholder="Ваш электронный адресс">
        <input class="form_item" v-model="form.password" type="password" placeholder="Ваш пароль">

        <button class="form_item btn" @click="login()" >Войти</button>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'HomeView',
  data: function() {
    return {
      regType: 1,

      form: {
        name: "",
        email: "",
        password: "",
        password_again: ""
      }
    }
  },
  methods: {
    setRegType(type) {
      this.regType = type
    },

    getLocalStorage() {
      try {
        return JSON.parse(localStorage.user)
      } catch (error) {
        return {
          _id: undefined
        }
      }
    },

    async login() {
      const resp = await fetch("https://srv.petiteweb.dev/api/profile/users/auth", {
          method: "post",
          headers: {
              "Content-Type": "application/json",
              Accept: "application.json"
          },
          body: JSON.stringify({
            email: this.form.email,
            password: this.form.password,
        })})
          
        const body = await resp.json()
        if (body.message !== "ok") {
          alert("произошла ошибка с API")
          return 
        }

        localStorage.setItem("user", JSON.stringify(body.data)),
        window.location = `/profile/${body.data._id}`

      console.log("LOGIN", this.form.email, this.form.password);
    },

    async registation() {
      if (this.form.password !== this.form.password_again) {
        alert("Пароли не совпадают")
        return
      }

      const resp = await fetch("https://srv.petiteweb.dev/api/profile/users/add", {
          method: "post",
          headers: {
              "Content-Type": "application/json",
              Accept: "application.json"
          },
          body: JSON.stringify({
            email: this.form.email,
            password: this.form.password,
            name: this.form.name
        })})
          
        const body = await resp.json()
        if (body.message !== "ok") {
          alert("произошла ошибка с API")
          return 
        }

        localStorage.setItem("user", JSON.stringify(body.data)),
        window.location = `/profile/${body.data._id}`
        // window.location.reload();
        

      console.log("Registration", this.form.email, this.form.name, this.form.password, this.form.password_again);
    }
  }
}
</script>

<style>
.home {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: space-between;
}

.image {
  width: 465px;
}

.image_logo {
  width: 323px;
  margin-bottom: -220px;
  margin-left: -200px;
  object-fit: contain;
}

.tabs {
  margin-top: 80px;
  width: 100%;

  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 20px;
}

.tab {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 18px;
  line-height: 22px;
  /* identical to box height */


  color: #FFFFFF;
  cursor: pointer;
}

.form {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: center;
  width: 284px;
}

.form_item {
  width: 284px;
  height: 46px;

  background: #292841;
  border-radius: 6px;
  border: none;
  outline: none;
  margin-top: 15px;
  padding-left: 20px;
  padding-right: 20px;
  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 18px;
  line-height: 22px;
  color: #FFFFFF;
}

.btn {
  cursor: pointer;
  margin-top: 40px;
}

.left_block {
  display: flex;
}

.rigth {
  position: relative;
  height: 100%;
}

.logo_second {
  width: 215px;
  position: absolute;
  right: 0;
  bottom: -100px;
}
</style>