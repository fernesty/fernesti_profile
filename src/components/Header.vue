<template>
  <div class="header" >
    <router-link to="/" class="title" >Imagine, Create, Share</router-link>

    <div class="right" >
      <router-link class="btn" to="/users">Users</router-link>

      <div v-if="getLocalStorage()._id !== undefined" >
        <button @click="goToProfile()" class="btn" >Profile</button>
      </div>
      <button v-if="getLocalStorage()._id !== undefined" class="btn" @click="logout()" >Log out</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Header',
  props: {
    msg: String
  },
  data: function() {

  },
  methods: {
    logout() {
      localStorage.user = undefined
      window.location = `/`
    },
    goToProfile() {
      window.location = `/profile/${this.getLocalStorage()._id}`
    },  
    getLocalStorage() {
      try {
        return JSON.parse(localStorage.user)
      } catch (error) {
        return {
          _id: undefined
        }
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.header {
  height: 78px;
  width: 100%;

  display: flex;
  justify-content: space-between;
  align-items: center;

  margin-bottom: 20px;
}

.title {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 30px;
  line-height: 36px;
  display: flex;
  align-items: center;

  color: #FFFFFF;
  text-decoration: none;
}

.btn {
  width: 81px;
  height: 44px;
  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 18px;
  line-height: 22px;
  /* identical to box height */


  color: #FFFFFF;
  border-radius: 6px;
  border: none;
  background: #292841;

  cursor: pointer;

  text-decoration: none;
  display: flex;
  align-items: center;
  justify-content: center;
}

.right {
  display: flex;
  gap: 20px;
}

</style>
