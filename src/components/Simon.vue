<template>
  <div class="simon">
    <div>
      <h1>Simon</h1>
      <div id="appColorButtons" class="simon__appColorButtons">
        <button 
          class="simon__colorButton simon__colorButton--blue"
          id="blue"
          @click="handleColorButton"
        ></button>
        <button 
          class="simon__colorButton simon__colorButton--red"
          id="red"
          @click="handleColorButton"
        ></button>
            <button
          class="simon__colorButton simon__colorButton--yellow"
          id="yellow"
          @click="handleColorButton"
        ></button>
        <button
          class="simon__colorButton simon__colorButton--green"
          id="green"
          @click="handleColorButton"
        ></button>
      </div>
    </div>
    <div class="simon__action">
      <h2>Раунд: {{ count }}</h2>
      <div>
        <button 
          class="simon__start"
          @click="start"
        >Старт</button>
      </div>
      <div v-if="lose">Извините вы проиграли после {{lose}} раундов!</div>
      <div class="simon__gameMode">
        <h3>Игровые опции</h3>
        <div>
          <input type="radio" value="1500" v-model="gameMode" :disabled="randomChain.length > 0" />
          <label>Легкий</label>
        </div>
        <div>
          <input type="radio" value="1000" v-model="gameMode" :disabled="randomChain.length > 0"/>
          <label>Средний</label>
        </div>
        <div>
          <input type="radio" value="500" v-model="gameMode" :disabled="randomChain.length > 0"/>
          <label>Сложный</label>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data:() => ({
    count: 0,
    step: 0,
    gameMode: 1500,
    lose: false,
    playerTurn: false,
    randomChain: [],
    playerChain: [],
  }),
  methods: {
    handleColorButton(event) {
      if (this.playerTurn) {
        this.audioSignal(event.target.id)
        this.opacityColorButton(event.target.id)
        this.playerChain.push(event.target.id)
        if (this.compareChain(this.step)) {
          this.step += 1
          if (this.step>=this.randomChain.length) {
            this.playerTurn = false
            setTimeout(()=>{
              this.addRandomStep()
              this.playRandomChain(this.randomChain)
              this.playerChain = []
              this.step = 0
            },1000)
          } 
        } 
        else {
          this.playerTurn = false
          this.lose = this.count
          this.randomChain = []
          this.playerChain = []
          this.count = 0    
        }
      }
    },
    start() {
      this.count = 0
      this.step = 0
      this.lose = false
      this.addRandomStep()
      this.playRandomChain(this.randomChain)
    },

    compareChain(step) {
      let random = this.randomChain.slice() 
      let player = this.playerChain.slice()
      return random[step] === player[step]
    },
    
    addRandomStep() {
      switch(Math.floor(Math.random()*4)){
        case 0 :
          this.randomChain.push('red')
          break
        case 1 :
          this.randomChain.push('green')
          break
        case 2 :
          this.randomChain.push('blue')
          break
        case 3 :
          this.randomChain.push('yellow')
          break
      }
      this.count += 1
    },
    playRandomChain(chain) {
        for(let i=0; i<chain.length; i++){
          setTimeout(()=>{
            this.audioSignal(chain[i])
            this.opacityColorButton(chain[i])
          }, i*this.gameMode)
        }
        setTimeout(()=>{
          this.playerTurn = true
        }, chain.length*this.gameMode)        
    },
    audioSignal(color) {
      const redAudio = new Audio('https://raw.githubusercontent.com/kellyk/javascript-simon/master/sounds/1.mp3');
      const greenAudio = new Audio('https://raw.githubusercontent.com/kellyk/javascript-simon/master/sounds/2.mp3');
      const blueAudio = new Audio('https://raw.githubusercontent.com/kellyk/javascript-simon/master/sounds/3.mp3');
      const yellowAudio = new Audio('https://raw.githubusercontent.com/kellyk/javascript-simon/master/sounds/4.mp3');

      switch(color){
        case 'red':
          redAudio.play()
          break;
        case 'green':
          greenAudio.play()
          break;
        case 'blue':
          blueAudio.play()
          break;
        case 'yellow':
          yellowAudio.play()
          break;
      }
    },
    opacityColorButton(color){
      document.getElementById(color).classList.add("active")
      setTimeout(()=>{
        document.getElementById(color).classList.remove("active")
      },300)
    }
  }
}
</script>

<style lang="scss">

body {
  display:flex;
  justify-content:center;
}

.simon {
  display: flex;
}

.simon__appColorButtons {
  width:300px;
  height:300px;
  border: 3px solid black;
  border-radius: 50%;

  .simon__colorButton {
    border:3px solid #333;
    width: 150px;
    height: 150px;
    outline:none;
    cursor:pointer;
    opacity: 0.6;
  }

  .simon__colorButton--red {
    background:#FF5643;
    border-radius:0% 100% 100% 0% / 0% 100% 0% 100%  ;
  }

  .simon__colorButton--green {
    background:#BEDE15;
    border-radius: 0% 100% 100% 0% / 0% 0% 100% 100% ;
  }

  .simon__colorButton--blue {
    background:dodgerblue;
    border-radius: 100% 0% 100% 0% / 100% 100% 0% 0% ;
  }

  .simon__colorButton--yellow {
    background:#FEEF33;
    border-radius: 0% 100% 0% 100% / 0% 0% 100% 100%  ;
  }

  .active {
    opacity: 1;
  }
}

.simon__action {
  width: 150px;
}

.simon__start {
  width: 5em;
  box-sizing: border-box;
  font-size: 1.4em;
  border-radius: 10px 10px 10px 10px;
  background: #6DABE8;
  border: none;
  padding: 0.3em 0.6em;
  margin-bottom: 20px;
}


.simon__gameMode {
  display: flex;
  flex-direction: column ;
  justify-content: center;
}

.simon__gameMode > div {
  display: flex;
  margin: 5px;
}

.simon__gameMode input {
  margin-right: 10px;
}

@media(max-width: 767px) {
  .simon {
    flex-direction: column;
    align-items: center;
  }
}

</style>