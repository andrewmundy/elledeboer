<template>
    <div class="main-view">
      <!-- <span v-html="importFont()"></span> -->
      <div class="header">
        <div 
          v-bind:style="renderStyle(
            {'background':'headerColor'},
            {'box-shadow':'headerSubColor'},
            {'font-family':'fontStyle'}
          )" 
          class="title hidden hidden-left" 
          v-infocus="'showElement'"
        >
          <span class="name" v-bind:style="renderStyle({'color':'fontColor'})">{{anObject.name}}</span>
          <h2 class="headline" v-bind:style="renderStyle({'color':'fontColor'})">
            {{anObject.name_description}}
            <br>
            {{anObject.location}}
            <br>
          </h2>
        </div> 
      </div>
      <img class="icon settings" src="../assets/icons/cogs.svg" v-on:click="toggle('edit')">
      <div class="admin_panel">
        <transition name="slide-fade">
          <edit 
            v-if="edit"
            v-bind="{
              anObject,
              toggle,
              isLogged,
              isLoggedIn,
              changeProp,
              name,
              name_description,
              contact,
              contact_description,
              title1,
              title1_rescription,
              instagram,
              twitter,
              facebook,
              messageEmail,
              info,
              social,
              image,
              banner,
              location,
              color,
              headerColor,
              headerSubColor,
              colorWindow,
              shadow,
              fontColor,
              fontStyle,
              fontImport,
              importFont
            }"
          />
        </transition>
      </div>



      <div class="spacer"></div>
      
      <img class="hidden hidden-right squiggle" v-infocus="'showElement-slow'" src="../assets/squiggle.svg">
        <h1 class="genre-titles">
          {{anObject.title1}}
        </h1>
        <h2 class="genre-quote hidden hidden-left" v-infocus="'showElement'">
          {{anObject.title1_description}}
        </h2>

      <img style="width:200px" id="marin"/>
      <projects/>

      <contact 
        v-bind="{
          contact, 
          contact_description, 
          changeProp, 
          anObject, 
          edit,
          toggle
          }" 
        />
    </div>
</template>

<style>
  button{
    background: rgba(0, 0, 0, 0.253);
    color: #ffffff4d;
    border-radius: 20px;
    border:none;
    padding:7px 10px;
    font-size: 0.8rem;
    font-weight: 600;
  }
  .closed{
    display:none;
  }
  #edit{
    text-align: left;
    padding: 0rem 1rem;
  }
  input{
    background:white;
    color:black;
    text-align: left;
  }
</style>

<script>
import Vue from 'vue'
import firebase from 'firebase'
import VueFire from 'vuefire'
import VueResource from 'vue-resource'

let config = {
  apiKey: 'AIzaSyBufg5IBGg1m8Z8Hew3kBf-KOSHK35VZfU',
  authDomain: 'elledeboer-7dddc.firebaseapp.com',
  databaseURL: 'https://elledeboer-7dddc.firebaseio.com',
  storageBucket: 'elledeboer-7dddc.appspot.com',
  messagingSenderId: '60590756705'
}

Vue.use(VueFire)
Vue.use(VueResource)

let app = firebase.initializeApp(config)
let db = app.database()
let storage = firebase.storage()
// let storageRef = storage.ref()
// let imageRef = storage.ref('images/marin.jpg').getDownloadURL()

export default {
  name: 'Main',
  data () {
    return {
      title1: '',
      title1_description: '',
      name: '',
      name_description: '',
      closed: true,
      login_closed: true,
      a: 'a',
      login: 'login',
      img: '',
      isLoggedIn: false,
      edit: false,
      imageArray: [],
      contact: '',
      contact_description: '',
      header_data: false,
      instagram: '',
      twitter: '',
      facebook: '',
      info: false,
      social: false,
      image: false,
      color: false,
      banner: '',
      location: '',
      headerColor: '',
      headerSubcolor: '',
      colorWindow: false,
      shadow: '',
      fontColor: '',
      fontStyle: '',
      fontData: ''
    }
  },
  firebase: {
    anObject: {
      source: db.ref('info'),
      asObject: true
    }
  },
  created: function () {
  },
  mounted: function () {
    this.fetchData()
    this.isLogged()
  },
  methods: {
    fetchData: function () {
      let popularity = 'https://www.googleapis.com/webfonts/v1/webfonts?sort=date'
      // let link = 'https://www.googleapis.com/webfonts/v1/webfonts?key=AIzaSyDCnMeRW7o8ZLMD6OqirbHumvUXuwuwahE'
      this.$http.get(popularity)
        .then(response => {
          console.log(response)
        }, response => {
          console.log('oops ' + response)
        })
    },
    // importFont () {
    //   let self = this
    //   if (self.anObject.fontImport) {
    //     let url = `<style> @import url('//fonts.googleapis.com/css?family=${self.anObject.fontImport}')</style>`
    //     self.anObject.fontStyle = self.anObject.fontImport
    //     console.log(url)
    //     return url
    //   }
    // },
    renderStyle (...args) {
      let style = {}
      let self = this
      args.map(function (arg) {
        let key = Object.keys(arg)[0]
        let value = Object.values(arg)[0]

        if (key === 'box-shadow') {
          style[key] = `${self.anObject.shadow} ${self.anObject[value]}`
        } else {
          style[key] = self.anObject[value]
        }
      })
      // console.log(style)
      return style
    },
    isLogged () {
      if (firebase.auth().currentUser) {
        this.isLoggedIn = true
      } else {
        this.isLoggedIn = false
      }
    },
    scrollMeTo (refName) {
      let element = this.$refs[refName]
      let top = element.offsetTop
      window.scrollTo(0, top)
    },
    changeProp: function (...args) {
      let self = this
      let updates = {}
      if (firebase.auth().currentUser) {
        args.map(function (arg) {
          updates[arg] = self.anObject[arg]
        })
        db.ref('info').update(updates)
      } else {
        self.banner = 'oops!'
      }
      return args
    },
    imagesRef (file) {
      storage.ref('images/' + file + '.jpg').getDownloadURL().then(function (url) {
        let img = document.getElementById(file)
        img.src = url
      }).catch(function (error) {
        console.log('oops ' + error)
      })
    },
    toggle (type) {
      if (this[type]) {
        this[type] = false
      } else if (!this[type]) {
        this[type] = true
      }
    }
  },
  directives: {
    infocus: {
      isLiteral: true,
      inserted: (el, binding, vnode) => {
        let f = () => {
          let rect = el.getBoundingClientRect()
          let inView = (
            rect.width > 0 &&
            rect.height > 0 &&
            rect.top >= 0 &&
            rect.bottom <= (window.innerHeight - 50 || document.documentElement.clientHeight)
          )
          if (inView) {
            el.classList.add(binding.value)
            window.removeEventListener('scroll', f)
          }
        }
        window.addEventListener('scroll', f)
        f()
      }
    }
  }
}
</script>