<template>
  <p>{{highscore}}</p>
  <p>Press 'c' on keyboard to cheat</p>
  <button v-if="!gameStart"  class="button" @click="startGame">Start</button>
  <div class="Tastertur" v-if="gameStart" >
    <button class="button" v-for="letter in letters" :key="letter" @click="buttonPressed(letter)">{{letter}}</button>
    
  </div>
  <p v-if="gameStart && cheat">{{currentWord}}</p>
  <p v-if="gameStart">{{anzeigeString}}</p>
  <br>
  <img :src="require('./img/' +fehler+'.jpg')" :alt="fehler">

  <table style="width:50%; margin:auto">
  <tr>
    <th>Date</th>
    <th>Highscore</th>
    
  </tr>
  <tr v-for="e in storage" :key="e">
    <td>{{e[0]}}</td>
    <td>{{e[1]}}</td>
    
  </tr>
  
</table>
</template>

<script>
import WordData from "../Aufgabe/wordlist.json";

export default {
  name: 'App',
  components: {
    
  },
  data:() => {
    return{
    home: window.location.href,
    words: WordData,
    storage: [],
    cheat: false,
    currentWord: String,
    fehler: 0,
    highscore:50,
    timer: Object,
    gefunden: "",
    anzeigeString: "",
    gameStart: false,
    letters : Array.from(Array(26)).map((e, i) => String.fromCharCode(i + 65)),
    }
  }, methods:{
    
    startGame(){
      // for(let i=0; i<localStorage.length; i++) {
      //     let key = localStorage.key(i);
      //     this.storage.push([key,localStorage.getItem(key)])
      //     alert(`${key}: ${localStorage.getItem(key)}`);
      //   }
      this.gameStart=true
      this.fehler = 0
      
      this.gefunden = ""
      this.anzeigeString = ""
      this.letters = Array.from(Array(26)).map((e, i) => String.fromCharCode(i + 65))
      this.currentWord= this.words[Math.floor(Math.random()* this.words.length)]
      for (let i = 0; i < this.currentWord.length; i++){
        this.anzeigeString += "_ "
      }
      try {
        clearInterval(this.timer)
      } catch (error) {
        console.log("YEYE ASS HAIRCUT")
      }
      this.timer = setInterval(this.scoreManager,1000,-1)
      
    },
   
    scoreManager(time){
      this.highscore += time
    },
    saveHighscore(){
      localStorage.setItem(new Date().toLocaleString(),this.highscore)
    },
    buttonPressed(letter){
      if(this.currentWord.includes(letter.toLowerCase())){
        this.scoreManager(10)
        this.gefunden += letter.toLowerCase()
        this.letters= this.letters.filter((w)=>{
          return w != letter 
        })
        this.anzeigeString = ""
        for (let i = 0; i < this.currentWord.length; i++){
          console.log(this.currentWord[i] + " " + this.gefunden)
          if(this.gefunden.includes(this.currentWord[i])){
            this.anzeigeString += this.currentWord[i]
          }else{ 
            this.anzeigeString += "_ "
          }
        }
        if (this.anzeigeString==this.currentWord){
          this.scoreManager(100)
          this.startGame()
          this.saveHighscore()
      }
    }else{
      if(this.fehler==10){
        alert("Fail")
        //Init
        clearInterval(this.timer)
        this.highscore = 50
        this.startGame()
      }else{
      this.fehler+=1
      this.letters= this.letters.filter((w)=>{
          return w != letter 
        })
      }
    }
      
    },
  },
  mounted() {
        
         window.addEventListener('keyup', event => {
            if (event.keyCode === 67) { 
            this.cheat = !this.cheat
            }
         })

        for(let i=0; i<localStorage.length; i++) {
          let key = localStorage.key(i);
          if ( !isNaN( Number(localStorage.getItem(key)))){

            this.storage.push([key,localStorage.getItem(key)])
          }
          // this.storage.push("2")
          // alert(this.storage)
          // alert(`${key}: ${localStorage.getItem(key)}`);
        }

      }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.Tastertur{
  display: flex;
  flex-direction: row;
  justify-content: center;
  
}

.button{
  margin: 5px;
  font-size: 20px;
}
</style>
