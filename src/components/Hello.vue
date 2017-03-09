<template>
  <div id="app">
    <section class="row">
      <div class="small-6 columns">
        <h1 class="text-center">玩家</h1>
        <div class="healthbar">
          <div class="healthbar text-center" style="background-color: green; margin: 0; color: white;" :style="{width: playerHealth + '%'}">
            {{ playerHealth }}
          </div> 
        </div> 
      </div>
      <div class="small-6 columns">
        <h1 class="text-center">妖怪</h1>
        <div class="healthbar">
          <div class="healthbar text-center" style="background-color: green; margin: 0; color: white;" :style="{width: monsterHealth + '%'}">
            {{ monsterHealth }}
          </div>
        </div>
      </div>
    </section>
    <section class="row controls" v-if="!gameIsRunning">
      <div class="small-12 columns">
        <button id="start-game" @click="startGame">开始游戏</button>
      </div> 
    </section>
    <section class="row controls" v-else>
      <div class="small-12 columns">
        <button id="attack" @click="attack">打</button>
        <button id="special-attack" @click="specialAttack">放大招</button>
        <button id="heal" @click="heal">治愈</button>
        <button id="give-up" @click="giveUp">投降</button>
      </div>
    </section>
    <section class="row log" v-if="turns.length > 0">
      <div class="small-12 columns">
        <ul>
          <li v-for="turn in turns" :class="{'player-turn' : turn.isPlayer, 'monster-turn' : !turn.isPlayer}">
            {{ turn.text }}
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  data () {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      gameIsRunning: false,
      turns: []
    }
  },
  methods: {
    startGame: function () {
      console.log('startGame')
      this.gameIsRunning = true
      this.playerHealth = 100
      this.monsterHealth = 100
      this.turns = []
    },
    attack: function () {
      var damage = this.calculateDamage(3, 10)
      this.monsterHealth -= damage
      this.turns.unshift({
        isPlayer: true,
        text: '玩家打击怪物, 怪物伤害: ' + damage
      })
      if (this.checkWin()) {
        return
      }
      this.monsterAttacks()
    },
    specialAttack: function () {
      var damage = this.calculateDamage(10, 20)
      this.monsterHealth -= damage
      this.turns.unshift({
        isPlayer: true,
        text: '玩家放大招打击，怪物伤害: ' + damage
      })
      if (this.checkWin()) {
        return
      }
      this.monsterAttacks()
    },
    heal: function () {
      if (this.playerHealth <= 90) {
        this.playerHealth += 50
      } else {
        this.playerHealth = 100
      }
      this.turns.unshift({
        isPlayer: true,
        text: '玩家治愈50'
      })
      this.monsterAttacks()
    },
    giveUp: function () {
      this.gameIsRunning = false
    },
    monsterAttacks: function () {
      var damage = this.calculateDamage(5, 12)
      this.playerHealth -= damage
      this.checkWin()
      this.turns.unshift({
        isPlayer: false,
        text: '怪物打玩家， 玩家伤害:' + damage
      })
    },
    calculateDamage: function (min, max) {
      return Math.max(Math.floor(Math.random() * max) + 1, min)
    },
    checkWin: function () {
      if (this.monsterHealth <= 0) {
        if (confirm('玩家win！ 再来一次')) {
          this.startGame()
        } else {
          this.gameIsRunning = false
        }
        return true
      } else if (this.playerHealth <= 0) {
        if (confirm('你输了！ 不服再来')) {
          this.startGame()
        } else {
          this.gameIsRunning = false
        }
        return true
      }
      return false
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.text-center {
  text-align: center;
}

.healthbar {
  width: 80%;
  height: 40px;
  background-color: #eee;
  margin: auto;
  transition: width 500ms;
}

.controls, .log {
  margin-top: 30px;
  text-align: center;
  padding: 10px;
  border: 1px solid #ccc;
  box-shadow: 0px 3px 6px #ccc;
}

.turn {
  margin-top: 20px;
  margin-bottom: 20px;
  font-weight: bold;
  font-size: 22px;
}

.log ul {
  list-style: none;
  font-weight: bold;
  text-transform: uppercase;
}

.log ul li {
  color: blue;
  background-color: #e4e8ff;
}

.log ul .monster-turn {
  color: red;
  background-color: #ffc0c1;
}

button {
  font-size: 20px;
  background-color: #eee;
  padding: 12px;
  margin: 10px;
}

#start-game {
  background-color: #aaffb0;
}

#start-game:hover {
  background-color: #76ff7e;
}

#attack {
  background-color: #ff7367;
}

#attack:hover {
  background-color: #ff3f43;
}

#special-attack {
  background-color: #ffaf4f;
}

#special-attack:hover {
  background-color: #ff9a2b;
}

#heal {
  background-color: #aaffb0;
}

#heal:hover {
  background-color: #76ff7e;
}

#give-up {
  background-color: #ffffff;
}

#give-up:hover {
  background-color: #c7c7c7;
}
</style>
