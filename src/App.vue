<template>
  <div class="container" >
    <div class="center" >

      <!-- <nav>
        <router-link to="/">Home</router-link> |
        <router-link to="/user/wed">About</router-link>
      </nav> -->
      <Header />
      <router-view/>
    </div>
  </div>
</template>


<script>
import Header from "@/components/Header.vue"

export default {
  name: "App",
  components: {
    Header
  }
}

</script>

<style>


@import url('https://fonts.googleapis.com/css2?family=Inter:wght@100;200;300;400;500;600;700;800;900&display=swap');

body {
  background-color: #1C1B29;
  color: white;
  margin: 0;
  padding: 0;
}

* {
  box-sizing: border-box;
}

.container {
  padding-left: 40px;
  padding-right: 40px;

  display: flex;
  justify-content: center;
  max-width: 100% !important;

}

.center {
  width: 100%;
  max-width: 1440px;
}

</style>
