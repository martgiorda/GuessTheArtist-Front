<template>
  <div id="main" class="hello">
    <div>
      <h1>{{"Who is this artist ?"}}</h1>
        <img v-bind:src="image" style="border : 2px black solid;" >
        <div></div>
        <h2>Is it :</h2>
        <div></div>
        <button v-for="choice in choices" 
          :key="choice"
          :id="choice.split(' ').join('_')"
          @click="sendAnswer({'image' : image, 'choice' : choice})"
          class="btn btn-primary mx-1">
          {{choice}}
        </button>
      </div>
      <br>
      <div>
      <button 
        id="musicPlayer"
        class="btn btn-warning mx-1"
        @click="getFirstHint()">
        {{"Get Hint 1"}}
      </button>
      <button 
        class="btn btn-secondary mx-1"
        @click="getSecondHint()">
        {{"Get Hint 2"}}
      </button>
      </div>
      <br>
      <div>
      <button v-for="genre in availableGenres" 
        :key="genre" 
        class="btn btn-info mx-1"
        @click="switchGenre(genre)">
        {{`Switch to ${genre}`}}
      </button>
      </div>
      <Modal :modalType="modalType" v-if="showModal" @close="onModalClose">
      </Modal>
  </div>
</template>

<script>

import axios from 'axios';

import Modal from './Modal.vue'

export default {
  name: 'GamePage',
  components : {
    Modal 
  },
  data : function () {
    return {
      choices : Array,
      image : String,
      currentArtist : null,
      guessedArtists : Array,
      topSong : Object,
      audio : null,
      isPlayingAudio : false,
      defaultGenre : "Pop",
      currentGenre : String,
      availableGenres : Array,
      showModal : false,
      goodAnswer : false,
      modalType : "failure"
      }
  },
  beforeMount() { 
    this.availableGenres = this.getAvailableGenres();
    this.currentGenre = this.availableGenres[0];
  },  
  mounted () {
    this.guessedArtists = [];
    this.getTest()
  },
  methods : {
    getAvailableGenres () {
      return ["Pop","Rock","Rap"]
    },
    setButtonDisabledState (state) {
      let buttons = document.getElementById('main').getElementsByTagName('button');
      for (let button of buttons) {
        button.disabled = state
      }
    },
    getTest () {

      axios
        .post(`http://localhost:3000/getTest`, {
                  headers:{
                        "Content-Type": "application/json"
                  },
                  body: {
                    guessedArtists : (this.guessedArtists.length > 0 ? this.guessedArtists : []),
                    genre : this.currentGenre
                  }
            })
        .then(response => {
            this.choices = response.data.choices
            this.image = response.data.image
            this.currentArtist = response.data.id
            this.setButtonDisabledState(false)
            return
        })
        .catch ((e) => {
            console.error(e.toString())
        })
    },
    sendAnswer (answer) {
    
      this.setButtonDisabledState(true)
      axios
        .post('http://localhost:3000/sendAnswer', {
                  headers:{
                        "Content-Type": "application/json"
                  },
                  body: answer
            })
        .then(response => {
          this.goodAnswer = response.data.goodAnswer
          if (this.goodAnswer) {
            this.guessedArtists.push(this.currentArtist)
            this.resetMusicButton()
            this.modalType = "success"
            this.showModal = true
            //this.getTest()
            
          }
          else {
            this.modalType = "failure"
            this.showModal = true
            this.setButtonDisabledState(false)
            return
          }

        })
        .catch ((e) => {
            console.log("pb frr ! : " + e.toString())
        })
    },
    getFirstHint() {

      if (this.isPlayingAudio) {
        this.resetMusicButton()
        return;
      }

      axios
        .get(`http://localhost:3000/getTopSong?id=${this.currentArtist}`)
        .then(response => {
            this.topSong = response.data
            this.audio = new Audio(response.data.preview); // path to file
            this.audio.play();
            let button = document.getElementById('musicPlayer')
            button.disabled = false;
            button.innerHTML = "Stop music"
            button.classList.add('btn-danger');     
            this.isPlayingAudio = true;
            return;
        })
        .catch ((e) => {
            console.error(e.toString())
        })
      
    },
    getSecondHint() {
      alert("top song : "+this.topSong.title)
    },
    resetMusicButton() {

      if (!this.isPlayingAudio) {
        return;
      }

      this.audio.pause();
      let button = document.getElementById('musicPlayer')
      button.innerHTML = "Get Hint"
      button.classList.remove('btn-danger');
      button.classList.add('btn-secondary');
      button.disabled = false;
      this.isPlayingAudio = false;
      return;
    },
    switchGenre(newGenre) {
      this.currentArtist = null;
      this.currentGenre = newGenre
      this.getTest(this.currentGenre);
    },
    onModalClose() {
      this.showModal = false
      if (this.goodAnswer) {
        this.getTest()
      }
    }
  }
}
</script> 

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
