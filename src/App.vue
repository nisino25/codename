<template>
  <div class="wrapper">
    <button @click="init()">start/reset</button>&nbsp;
    <button @click="showingConfirmation()">showing:{{showing}}</button>&nbsp;
    <br><br>
    <span style="font-size:150%">Blue: {{blueCount}}/9: Red: {{redCount}}/8</span> <br>
    <strong style="font-size:200%"  v-if="winner">The winner is {{winner}}!</strong>


    <!-- <template v-for="(item, index) in gameData" :key="index">
      <span>{{index}}: {{item.title}}</span><br>
    </template> -->

    <hr>
    
    
      <div class="wordsList row" v-if="gameData?.length >= 1" >

        <template v-for="(item, index) in gameData" :key="index">
          <div class="card" @click="clicking(item)" :class="getStyle(item)">

            <!-- <i v-if="!item.opened" class='fas fa-skull-crossbones' style='font-size:35px;color:red; margin-top: 5px'></i> -->

            <div class="container" > 
              <span class="title" v-if="!item.opened || isGameOver">{{item.title}}</span>
              <!-- <span class="title">{{item.team}}</span> -->
              

              <!-- <template v-if="canISeeYourRole(player)">
                <span  style="color:red">{{player.team}} <br> {{player.role}}</span>
              </template> -->

              <!-- <template v-else> -->
                
                <!-- <span v-if="myPlayer?.name == player.name" :style="getColor(player.team)">{{item.team}}</span><br>
                <span v-if="myPlayer?.name == player.name">{{player.role}}</span><br> -->
              <!-- </template> -->

              
            </div>
          </div>

          <div v-if="(index+1) % 5==0" cals="line-break"></div>
        </template>

      </div>

  </div>
</template>

<script>
import {wordsList} from './const/wordsList.js'

export default {
  name: 'App',
  components: {
    // HelloWorld
  },

  data(){
    return{
      wordsList,
      gameData: [],
      showing: false,
      winner: undefined,
      isGameOver: false,

    }
  },
  methods:{

    init(){
      this.clean()

      let count =0 
      while(count < 25){
        // this.gameData.push(this.getRandomValue)
        let word = this.getRandomValue('word')
        if(!word.used){
          this.gameData.push(word)
          word.used = true
          count++
        }
      }

      count = 0
      while(count < 18){
        // this.gameData.push(this.getRandomValue)
        let word = this.getRandomValue('gameData')
        if(!word.team){
          if(count< 9 ){
            word.team = 'blue'
            count++
          }else if(count< 17){
            word.team = 'red'
            count++
          }else{
            word.team = 'bomb'
            count++
          }
        }
      }

      // console.log(this.gameData)
    },

    clean(){
      this.gameData= []
      for(let i in this.wordsList){
        this.wordsList[i].used =  false
        this.wordsList[i].opened = false
        this.wordsList[i].team = undefined
      }
    },

    getRandomValue(item){
      if(item == 'word'){
        return this.wordsList[Math.floor(Math.random()* this.wordsList.length)]; 
      }
      if(item == 'gameData'){
        return this.gameData[Math.floor(Math.random()* this.gameData.length)]; 
      }
    },

    getStyle(item){
      if(this.showing || item.opened) {

        if(item.team == 'red'){
          return 'showing-red'
        }

        if(item.team == 'blue'){
          return 'showing-blue'
        }

        if(item.team == 'bomb'){
          return 'showing-bomb'
        }

        if(item.team == undefined){
          return 'opa'
        }
      }

    },

    showingConfirmation(){
      if(this.showing) {
        this.showing = false
        return
      }
      if(confirm( `Are you sure you wanna see the ansewrs? `)){
        this.showing = true
      }
    },

    // ------------------------------
    clicking(item){
      if(item.opened) return
      if(this.isGameOver) return
      if(confirm( `Are you opening ${item.title}? `)){
        item.opened = true
        if(item.team =='bomb'){
          this.winner = 'bomb'
        }
      }
    }
  },

  mounted(){
    console.clear()
    console.log('mounted')

    let list = []

    for(let i in this.wordsList){
      list.push({title: this.wordsList[i], used: false, opened: false, team: undefined})
    }

    this.wordsList = list
    // console.log(this.wordsList)
  },
  computed:{
    redCount(){
      let count = 0
      for(let i in this.gameData){
        let item = this.gameData[i]
        if(item.team == 'red' && item.opened){
          count++
        }
      }
      return count
    },

    blueCount(){
      let count = 0
      for(let i in this.gameData){
        let item = this.gameData[i]
        if(item.team == 'blue' && item.opened){
          count++
        }
      }
      return count
    },
  },

  watch:{
    winner(){
      this.isGameOver = true
      if(this.winner == 'bomb'){
        alert(`You just picked the bomb! The game is ovet`)
        return
      }
      alert(`${this.winner} just won!`)
    },
    redCount(){
      if(this.redCount == 8){
        this.winner = 'red'
      }
    },
    blueCount(){
      if(this.blueCount == 9){
        this.winner = 'blue'
      }
    },
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-top: 20px;
}

.wrapper{
  width: 90%;
  margin: 0 auto;
}

/*  cards */
.wordsList{
  padding-right: 5px;
  padding-left: 5px;
  color: #2c3e50;
  margin-top: 20px;
  /* position: relative; */
  /* background-color: red; */
  display: flex;
  /* width: 100%; */

  /* background: tomato; */
  /* display: flex; */
  flex-flow: row wrap;
  align-content: space-between;
  justify-content: space-between;

}
.card {
  background-color: #939597;
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
  transition: 0.3s;
  width: 18%;
  border-radius: 5px;
  margin-left: 1.5%;

  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2); /* this adds the "card" effect */
  /* padding: 0px; */
  text-align: center;
  background-color: #f1f1f1;
  margin-bottom: 30px;
  /* height: auto; */
  height:30px;
  font-size: 80%;
  padding-top: 30px;
  padding-bottom: 30px;
  
}

.line-break {
  width: 100%;
}

/* .card img {
  f
  margin-top: 5px;
  margin-bottom: 0px;
  border-radius: 5px 5px 0 0;
  height: 30px;
  width: auto;
} */

.card .title{
  font-size: 180%;
  font-weight: bold;
  margin-bottom: 10px;
  padding: 10px 10px;

  /* color: red */
}

.targeted{
  background-color: #3CB371;
}

.showing-blue{
  background-color: cornflowerblue;
  color:white;
}

.showing-red{
  background-color: crimson;
  color:white;
}

.showing-bomb{
  background-color: black;
  color:white;
}

.opa{
  background-color: mediumseagreen;
  color:white;
}



</style>
