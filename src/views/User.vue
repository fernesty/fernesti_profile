<template>
  <div class="about">

    <div v-if="isModal" class="modal_container" >
      <div class="modal">
        <p class="modal_title">Создание проекта</p>

        <div class="modal_columnt" >
          <input v-model="modalData.name" class="modal_input_short" placeholder="Название" type="text">
          <input v-model="modalData.date" class="modal_input_short" placeholder="Дата" type="date" >
        </div>

        <div class="modal_columnt" >
          <input v-model="modalData.link" class="modal_input_short" placeholder="Ссылка" type="text">
          <input v-model="modalData.linkOnImage" class="modal_input_short" placeholder="Ссылка на картинку" type="text">
        </div>

        <textarea v-model="modalData.description" class="modal_input_textarea" />

        <div class="modal_columnt" >
          <button class="modal_input_bnt" @click="saveProject()" >Сохранить</button>
          <button class="modal_input_bnt" @click="setModal(false)" >Отменить</button>
        </div>
      </div>
    </div>

    <div class="left">
      <div class="account_container">
        <img src="../assets/Avatar.png" alt="">

        <p class="account_container__name" >{{ userData.name }}</p>

        <div class="icons">
          <p class="icons_text" >{{ like }}</p>
          <img class="icons_icon" src="../assets/icons/like.png" >
          <p class="icons_text" >{{ repost }}</p>
          <img class="icons_icon" src="../assets/icons/reports.png" >
          <p class="icons_text" >{{ followers }}</p>
          <img class="icons_icon" src="../assets/icons/user.png" >
        </div>

      </div>    
      
      <button class="account_container_bottom_btn" >
        {{ userData.email }}
      </button>
    </div>

    <div class="rigth">
      <div class="title"> Ваше портфолио </div>

        <div v-if="userData.portfolio && userData.portfolio.length > 0"  >
        <div v-for="item in userData.portfolio" class="project_card" >
          <div class="project_card__left" >
            <p class="project_card__title" >{{ item.title }}</p>
            <img 
              class="project_card__image" 
              :src="item.main_image" >

            <button v-if="isUser" class="project_card__remove_btn" @click="removePortfolio(item._id)" >Удалить</button>
            <p class="date_of_create"  >Дата создания: {{ new Date(item.date).getFullYear()  }} / {{ new Date(item.date).getMonth()  }} / {{ new Date(item.date).getDate()  }}</p>
          </div>
          <div class="project_card__right" >
            <div>
              <p class="project_card__description" >Описание</p>
              <p class="project_card__description_more" >{{ item.description }}</p>
            </div>
            <a :href="item.main_image" class="project_card__link_art" >Прямая ссылка на арт</a>
          </div>
        </div>
      </div>
      <div v-else class="not_projects" >
        Портфолио пустое
      </div>

      <button class="add_card"  v-if="isUser" @click="setModal(true)" >Создать</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'User',
  props: {
    id: String
  },
  data: function() {
    return {
      isModal: false,
      userData: {},

      modalData: {
        name: "",
        date: "",
        link: "",
        linkOnImage: "",
        description: ""
      },

      like: 0,
      followers: 0,
      repost: 0,
    }
  },
  methods: {
    setModal(val) {
      this.isModal = val;
    },

    async getUser() {
      const id = this.$route.params.id

      const resp = await fetch(`https://srv.petiteweb.dev/api/profile/users/${id}`)
      const body = await resp.json()

      if (body.message !== "ok") alert("Ошибка при получения пользователя")
      else {
        this.userData = body.data
      }
    },

    async removePortfolio(projectId) {
      const id = this.$route.params.id

      const resp = await fetch(`https://srv.petiteweb.dev/api/profile/users/project/remove/${id}`, {
          method: "PATCH",
          headers: {
              "Content-Type": "application/json",
              Accept: "application.json"
          },
          body: JSON.stringify({
            _id: projectId
          })
        })  

        
        this.getUser()
    },

    async saveProject() {
      const id = this.$route.params.id

      const resp = await fetch(`https://srv.petiteweb.dev/api/profile/users/project/add/${id}`, {
          method: "PATCH",
          headers: {
              "Content-Type": "application/json",
              Accept: "application.json"
          },
          body: JSON.stringify({
            title: this.modalData.name,
            link: this.modalData.link,
            main_image: this.modalData.linkOnImage,
            description: this.modalData.description,
            tags: [],
            date: this.modalData.date
          })
        })  
        const body = await resp.json()

        this.setModal(false)
        this.getUser()
    }
  },
  mounted() {
    this.getUser()

    this.followers = Math.floor(Math.random() * 150);
    this.repost = Math.floor(Math.random() * 20);
    this.like = Math.floor(Math.random() * 300);

    
    const setLike = async () => {
      this.like = this.like + Math.floor(Math.random() * 5)

      setTimeout(() => setLike(), Math.floor(Math.random() * 4) * 1000)
    }

    setLike()

    const setReposts = async () => {
      this.repost = this.repost + Math.floor(Math.random() * 5)

      setTimeout(() => setReposts(), Math.floor(Math.random() * 4) * 1000)
    }

    setReposts()

    const setFollowers = async () => {
      this.followers = this.followers + Math.floor(Math.random() * 5)

      setTimeout(() => setFollowers(), Math.floor(Math.random() * 4) * 1000)
    }

    setFollowers()

  },
  computed: {
    isUser() {
      return window.location.pathname === `/profile/${this.getLocalStorage._id}`
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
  }
}
</script>

<style>

.modal_container {
  position: absolute;

  top: 0;
  left: 0;
  width: 100vw;
  height: 100Vh;
  background: rgba(0, 0, 0, 0.5);

  display: flex;
  justify-content: center;
  align-items: center;

  z-index: 999;
}

.modal_columnt {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 30px;
  margin-top: 20px;
}

.modal {
  width: 572px;
  min-height: 398px;

  background: #1C1B29;
  border-radius: 6px;
  padding: 20px;
  padding-top: 0;
}

.modal_title {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 30px;
  line-height: 36px;
  display: flex;
  align-items: center;

  color: #FFFFFF;
}

.modal_input_short {
  width: 100%;
  height: 44px;
  left: 282px;
  top: 387px;

  background: #292841;
  border-radius: 6px;

  border: none;

  font-family: 'Inter';
  font-style: normal;
  font-weight: 400;
  font-size: 18px;
  line-height: 22px;
  /* identical to box height */

  display: flex;
  align-items: center;

  color: rgba(255, 255, 255, 0.5);

  outline: none;
  padding: 5px;
}

.modal_input_textarea {
  padding: 5px;
  margin-top: 30px;
  width: 100%;
  height: 138px;
  left: 282px;
  top: 526px;

  background: #292841;
  border-radius: 6px;

  font-family: 'Inter';
  font-style: normal;
  font-weight: 400;
  font-size: 18px;
  line-height: 22px;
  /* identical to box height */

  display: flex;
  align-items: center;

  color: rgba(255, 255, 255, 0.5);
  border: none;
  resize: none;
  outline: none;
}

.modal_input_bnt {
  background: #292841;
  border-radius: 6px;
  font-family: 'Inter';
  font-style: normal;
  font-weight: 400;
  font-size: 18px;
  line-height: 22px;
  color: rgba(255, 255, 255, 0.5);
  border: none;
  height: 38px;
  cursor: pointer;
  width: 100%;
}


.about {
  display: flex;
  justify-content: space-between;
}

.left {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
}

.rigth {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: flex-start;

  width: 100%;
  margin-left: 30px;
}

.rigth .title {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 30px;
  line-height: 36px;

  color: #FFFFFF;
}

.account_container {
  width: 332px;
  height: 346px;
  background: #292841;
  border-radius: 6px;

  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;

  padding: 20px;
}

.account_container__name {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 18px;
  line-height: 22px;

  color: #FFFFFF;
}

.project_card {
  margin-top: 20px;
  min-height: 356px;

  display: flex;
  justify-content: space-between;
  align-items: flex-start;

  padding-left: 40px;
  padding-right: 40px;
  padding-top: 0px;
  padding-bottom: 20px;

  background: #292841;
  border-radius: 6px;

  position: relative;

}


.project_card__left {
  
}

.project_card__right {
  margin-left: 21px;
  padding-top: 35px;

  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: space-between;
  height: 100%;

  min-width: 600px;
  width: 100%;
}

.project_card__description {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 18px;
  line-height: 22px;
  /* identical to box height */


  color: #FFFFFF;

}

.project_card__description_more {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 400;
  font-size: 14px;
  line-height: 17px;

  color: #FFFFFF;

}

.project_card__title {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 18px;
  line-height: 22px;

  color: #FFFFFF;
}

.project_card__image {
  width: 200px;
  height: 200px;
  object-fit: cover;
  border-radius: 6px;
}

.project_card__remove_btn {
  width: 200px;
  height: 32px;
  background: #ED5E69;
  border-radius: 6px;
  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 16px;
  line-height: 19px;

  color: #FFFFFF;
  display: flex;
  justify-content: center;
  align-items: center;
  border: none;
  margin-top: 15px;
  cursor: pointer;
}

.date_of_create {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 14px;
  line-height: 17px;
  color: #FFFFFF;
  margin-top: 15px;
}

.project_card__link_art {

  position: absolute;
  bottom: 10px;
  right: 20px;

  font-family: 'Inter';
  font-style: italic;
  font-weight: 400;
  font-size: 14px;
  line-height: 17px;
  width: 100%;
  cursor: pointer;
  text-decoration: none;
  color: #FFFFFF;
  text-align: end;
}

.icons {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 10px;
}

.icons_icon {
  widows: 18px;
  height: 18px;
}

.icons_text {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 18px;
  line-height: 22px;

  color: #FFFFFF;
  width: 40px;
  text-align: center;
}

.account_container_bottom_btn {
  width: 100%;
  height: 55px;

  background: #292841;
  border-radius: 6px;
  border: none;
  margin-top: 15px;

  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 18px;
  line-height: 22px;
  /* identical to box height */


  color: #FFFFFF;
}

.not_projects {
  margin-top: 200px;
  width: 100%;
  text-align: center;

  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 18px;
  line-height: 22px;
  /* identical to box height */


  color: #FFFFFF;

}

.add_card {
  width: 284px;
  height: 46px;
  background: #292841;
  border-radius: 6px;

  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 18px;
  line-height: 22px;
  color: #FFFFFF;
  border: none;

  margin-top: 20px;
}

</style>

