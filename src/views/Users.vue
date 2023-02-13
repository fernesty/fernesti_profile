<template>
  <div class="users">
    
    <router-link :to="`/profile/${user._id}`" v-for="user in usersData" class="user">
      <div class="avatar">
        <span v-if="user.name && user.name !== ''"  >
          {{user.name[0]}}
        </span>
        <span v-if="!user.name && user.email && user.email !== ''"  >
          {{user.email[0]}}
        </span>
      </div>
      
      <div class="description" >
        <p class="title" >{{ user.name }}</p>
        <p class="sub_title" >{{ user.email }}</p>
      </div>
    </router-link>
    
  </div>
</template>

<script>
export default {
  name: 'Users',
  props: {
    id: String
  },
  data: function() {
    return {
      usersData: {}
    }
  },
  methods: {
    async getUsers() {
      const id = this.$route.params.id

      const resp = await fetch(`https://srv.petiteweb.dev/api/profile/users`)
      const body = await resp.json()

      if (body.message !== "ok") alert("Ошибка при получения пользователя")
      else {
        this.usersData = body.data
      }
    }
  },
  mounted() {
    this.getUsers()
  }
}
</script>

<style>

.user {
  margin-top: 20px;
  display: flex;
  justify-content: flex-start;
  align-items: center;

  text-decoration: none;
}

.avatar {
  width: 60px;
  height: 60px;

  background: rgb(241,130,163);
  background: linear-gradient(90deg, rgba(241,130,163,1) 0%, rgba(130,71,223,1) 100%);

  border-radius: 100%;

  font-family: 'Inter';

  display: flex;
  justify-content: center;
  align-items: center;

  font-style: normal;
  font-weight: 700;
  font-size: 21px;
  line-height: 25px;

  color: #FFFFFF;

}

.description {
  margin-left: 10px;

  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: center;

  height: 20px;
}

.title  {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 500;
  font-size: 16px;
  line-height: 19px;

  color: #FFFFFF;

}
.sub_title  {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 400;
  font-size: 12px;
  line-height: 15px;
  /* identical to box height */


  margin-top: -5px;
  color: rgba(255, 255, 255, 0.8);

}

</style>