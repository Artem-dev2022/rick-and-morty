<script>
import axios from 'axios'
import NavMenu from './components/NavMenu.vue'
import PersonCard from './components/PersonCard.vue'

export default {
  data() {
    return {
      characters: [],
      charactersData: null,
      inputValue: '',
      emptyList: false,
      clearInput: false,
      nextPage: null,
      BASE_URL: 'https://rickandmortyapi.com/api/character'
    }
  },
  components: { NavMenu, PersonCard },
  computed: {
    charactersLength() {
      if (!this.charactersData) return
      return this.emptyList ? 0 : this.charactersData.data.info.count
    },
    charactersFormat(){
      let count = this.charactersLength % 100
      if (count >= 10 && count <=20) return this.charactersLength + ' персонажей'

      count = this.charactersLength % 10
      if (count === 1) {
          return this.charactersLength + ' персонаж'
      } else if (count >= 2 && count <= 4) {
          return this.charactersLength + ' персонажа'
      } else {
          return this.charactersLength + ' персонажей'
      }
    }
  },
  methods: {
    async getCharacters(url = this.BASE_URL, name = '', page = 1){
      const data = await axios.get(url, {
          params: {
            name: name,
            page: page
          }
        })
        .then(res => [this.charactersData = res, 
          this.characters = res.data.results,
          this.nextPage = res.data.info.next])
        .then(() => this.emptyList = false)
        .catch(() => this.emptyList = true)
    },
    getValue(){
      this.getCharacters(this.BASE_URL, this.inputValue)
    },
    resetInput(){
      this.inputValue = ''
      this.getCharacters(this.BASE_URL)
    },
    async getMoreCharacters(){
      const data = await axios.get(this.nextPage)
      .then(res => [this.characters = [...this.characters, ...res.data.results],
        this.nextPage = res.data.info.next])
    }
  },
  created() {
    this.getCharacters();
  }
}
</script>

<template>
  <div class="container">
    <h1 class="title">Персонажи</h1>
    <label for="input" class="search">
        <svg class="search__icon" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none">
            <path fill-rule="evenodd" clip-rule="evenodd" d="M13.7929 13.7929C14.1834 13.4024 14.8166 13.4024 15.2071 13.7929L20.7071 19.2929C21.0976 19.6834 21.0976 20.3166 20.7071 20.7071C20.3166 21.0976 19.6834 21.0976 19.2929 20.7071L13.7929 15.2071C13.4024 14.8166 13.4024 14.1834 13.7929 13.7929Z" fill="#C1C2C7"/>
            <path fill-rule="evenodd" clip-rule="evenodd" d="M10 5C7.23858 5 5 7.23858 5 10C5 12.7614 7.23858 15 10 15C12.7614 15 15 12.7614 15 10C15 7.23858 12.7614 5 10 5ZM3 10C3 6.13401 6.13401 3 10 3C13.866 3 17 6.13401 17 10C17 13.866 13.866 17 10 17C6.13401 17 3 13.866 3 10Z" fill="#C1C2C7"/>
        </svg>
        <input @input="getValue" v-model="inputValue" class="search__input" type="text" id="input" placeholder="Поиск">
    </label>
    <p class="quantity">{{charactersFormat}}</p>

    <div class="wrapper">
      <ul class="list" v-if="!emptyList">
        <PersonCard v-for="item of characters" :key="item.id" :item="item"/>
        <li v-show="nextPage">
          <button class="list__btn" @click="getMoreCharacters">
            Показать еще
            <svg xmlns="http://www.w3.org/2000/svg" width="11" height="20" viewBox="0 0 11 20" fill="none">
              <g clip-path="url(#clip0_3_203)">
              <path fill-rule="evenodd" clip-rule="evenodd" d="M10.2902 13.574C10.3567 13.6514 10.4095 13.7434 10.4455 13.8446C10.4815 13.9458 10.5 14.0543 10.5 14.1639C10.5 14.2735 10.4815 14.382 10.4455 14.4832C10.4095 14.5845 10.3567 14.6764 10.2902 14.7538L6.00541 19.7527C5.93909 19.8303 5.86027 19.8918 5.77351 19.9338C5.68676 19.9758 5.59374 19.9975 5.49981 19.9975C5.40589 19.9975 5.31287 19.9758 5.22611 19.9338C5.13936 19.8918 5.06054 19.8303 4.99421 19.7527L0.709427 14.7538C0.575333 14.5973 0.5 14.3852 0.5 14.1639C0.5 13.9427 0.575333 13.7305 0.709427 13.574C0.843523 13.4176 1.02539 13.3297 1.21503 13.3297C1.40467 13.3297 1.58654 13.4176 1.72064 13.574L5.49981 17.9847L9.27899 13.574C9.34533 13.4965 9.42413 13.4349 9.51089 13.3929C9.59766 13.3509 9.69066 13.3293 9.7846 13.3293C9.87853 13.3293 9.97154 13.3509 10.0583 13.3929C10.1451 13.4349 10.2239 13.4965 10.2902 13.574Z" fill="#3C4061"/>
              <path fill-rule="evenodd" clip-rule="evenodd" d="M5.50014 0C5.68954 1.28713e-08 5.87118 0.0877784 6.00511 0.244025C6.13902 0.400272 6.21427 0.612187 6.21427 0.833152V18.3293C6.21427 18.5503 6.13902 18.7622 6.0051 18.9185C5.87118 19.0747 5.68954 19.1625 5.50014 19.1625C5.31074 19.1625 5.1291 19.0747 4.99517 18.9185C4.86124 18.7622 4.78601 18.5503 4.78601 18.3293V0.833152C4.78601 0.612187 4.86124 0.400272 4.99517 0.244025C5.1291 0.0877784 5.31074 -1.28713e-08 5.50014 0Z" fill="#3C4061"/>
              </g>
              <defs>
              <clipPath id="clip0_3_203">
              <rect width="10" height="20" fill="white" transform="translate(0.5)"/>
              </clipPath>
              </defs>
            </svg>
          </button>
        </li>
      </ul>
      <p v-else class="empty">Не удалось найти персонажей с таким именем</p>
    
      <div v-show="inputValue.length" class="reset">
        <button @click="resetInput" class="reset__btn">Сбросить поиск</button>
      </div>
    </div>
    <NavMenu class="menu"/>
  </div>
</template>

<style scoped>
.title {
  padding: 16px 0 24px 0;
  color: #221A51;
  font-size: 20px;
  font-weight: 400;
  line-height: 24px;
  letter-spacing: -0.2px;
  text-align: center;
}
.search {
    margin: 0 auto 10px auto;
    padding: 8px 12px;
    display: flex;
    gap: 8px;
    width: 325px;
    border-radius: 100px;
    background: #FFF;
    cursor: pointer;
}
.search__input {
    width: 269px;
    border: none;
    color: #C1C2C7;
    font-size: 17px;
    font-weight: 400;
    line-height: 24px;
}
.quantity {
  margin-bottom: 10px;
  color: #C1C2C7;
  font-size: 15px;
  font-weight: 400;
  letter-spacing: -0.327px;
  text-align: center;
}
.wrapper {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: 630px;
}
.list {
  display: flex;
  flex-direction: column;
  flex-shrink: 2;
  gap: 10px;
  margin: 0 auto 10px auto;
  list-style: none;
  overflow-y: scroll;
  width: 325px;
  max-height: 535px;
}
.list::-webkit-scrollbar {
  width: 0;
}
.list__btn {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  height: 56px;
  padding: 15px 65.5px;
  border-radius: 10px;
  border: 1px solid #F4F5F5;
  background: #FFF;
  color: #3C4061;
  font-size: 17px;
  font-weight: 700;
  letter-spacing: -0.327px;
}
.empty {
  margin: 48px auto 0 auto;
  width: 203px;
  color:#C1C2C7;
  text-align: center;
  font-size: 15px;
  font-weight: 400;
  letter-spacing: -0.327px;
}
.menu {
  position: absolute;
  bottom: 0px;
}
.reset {
  padding-top: 11px;
  flex-shrink: 0;
  width: 375px;
  height: 160px;
  border-radius: 10px 10px 0px 0px;
  background: #FFF;
  box-shadow: 0px 0px 13px 0px rgba(22, 22, 23, 0.26);
}
.reset__btn {
  display: block;
  margin: 0 auto;
  width: 325px;
  height: 56px;
  border-radius: 10px;
  background: #CB99FA;
  color: #FFF;
  font-size: 20px;
  font-weight: 900;
  line-height: 24px;
}
</style>
