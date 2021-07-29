<template>
  <main id="app-wrapper">
    <div class="container">
      <div class="calculator">
        <div class="segments">
          <div class="seg" v-for="(seg, index) in segments" :key="index">{{seg}}</div>
        </div>
        <input class="display-input" type="text" :value="display" :style="'font-size:' + fontSize + 'px'" disabled>

        <div class="buttons">
          <button type="button" class="operator" value="+" @click="opt">+</button>
          <button type="button" class="operator minus" value="-" @click="opt">-</button>
          <button type="button" class="operator multiply" value="x" @click="opt">x</button>
          <button type="button" class="operator" value="÷" @click="opt">÷</button>

          <button type="button" class="num" value="7" @click="num">7</button>
          <button type="button" class="num" value="8" @click="num">8</button>
          <button type="button" class="num" value="9" @click="num">9</button>

          <button type="button" class="num" value="4" @click="num">4</button>
          <button type="button" class="num" value="5" @click="num">5</button>
          <button type="button" class="num" value="6" @click="num">6</button>

          <button type="button" class="num" value="1" @click="num">1</button>
          <button type="button" class="num" value="2" @click="num">2</button>
          <button type="button" class="num" value="3" @click="num">3</button>

          <button type="button" class="num" value="0" @click="num">0</button>
          <button type="button" class="decimal" value="." @click="decimal">.</button>
          <button type="button" class="ac" value="ac" @click="clear">AC</button>

          <button type="button" class="equal-sign" value="=" @click="result">=</button>
        </div>
      </div>
      <ul class="history">
        <li v-for="(item,index) in history" :key="index">{{item.firstNum}} {{item.operator}} {{item.secondNum}} = {{item.result}}</li>
      </ul>
    </div>
  </main>
</template>

<script>
export default {
  name: 'App',
  data () {
    return {
      maxHistoryLength: 10,
      history: [],
      segments: [],
      display: '0'
    }
  },
  created () {
    localStorage.getItem('calcHistory') || localStorage.setItem('calcHistory', JSON.stringify([]))
    const history = JSON.parse(localStorage.getItem('calcHistory'))
    this.history = history
  },
  methods: {
    check () {
      const operators = ['+', '-', 'x', '÷']
      return operators.includes(this.segments[this.segments.length - 1])
    },

    result (e) {
      if (this.segments.length === 0) {
        return
      }
      if (this.check()) {
        this.segments.push(this.display)

        const expression = this.segments[1]
        switch (expression) {
          case '+':
            this.display = parseFloat(this.segments[0]) + parseFloat(this.segments[2])
            break
          case '-':
            this.display = parseFloat(this.segments[0]) - parseFloat(this.segments[2])
            break
          case 'x':
            this.display = parseFloat(this.segments[0]) * parseFloat(this.segments[2])
            break
          case '÷':
            this.display = parseFloat(this.segments[0]) / parseFloat(this.segments[2])
            break
        }
      }

      this.save()
    },

    save () {
      if (this.history.length >= this.maxHistoryLength) {
        this.history.splice(1, 1)
      }
      const obj = {
        firstNum: this.segments[0],
        operator: this.segments[1],
        secondNum: this.segments[2],
        result: this.display
      }

      this.history.push(obj)

      localStorage.removeItem('calcHistory')
      localStorage.setItem('calcHistory', JSON.stringify(this.history))
    },

    decimal (e) {
      if (this.display[this.display.length - 1] === '.') {
        return
      }
      if (this.display === '0') {
        this.display = e.target.value
      } else {
        this.display += e.target.value
      }
    },

    num (e) {
      if (this.segments.length === 3) {
        this.segments = []
        this.display = '0'
      }

      if (this.display === '0') {
        this.display = e.target.value
      } else {
        this.display += e.target.value
      }
    },

    opt (e) {
      if (this.segments.length === 3) {
        this.segments = []
      }

      if (this.check()) {
        this.segments.splice(-1, 1, e.target.value)
      } else {
        this.segments.push(this.display)
        this.segments.push(e.target.value)
      }

      this.display = '0'
      this.operator = e.target.value
    },
    clear () {
      this.display = '0'
      this.segments = []
    }
  },
  computed: {
    fontSize () {
      if (this.display.length <= 23) {
        return 33 - (26 / (25 - this.display.length))
      } else {
        return 18 - (120 / (121 - this.display.length))
      }
    }
  }
}
</script>

<style lang="scss">
:root {
  --yellow: #ffc600;
  --black: #272727;
  --gray: #555;
  --white: #eee;
}

*, *::before, *::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html {
  font-size: 16px;
}

#app-wrapper {
  width: 100vw;
  height: 100vh;
  background-color: #ccc;
  display: flex;
  justify-content: center;
  align-items: center;
}

.container {
  display: flex;
}

button {
  height: 60px;
  font-size: 2rem;
  color: var(--gray);
  border: 0;
  border-radius: .5em;
  background-color: var(--black);
}

button.operator {
  color: var(--yellow);
}
.minus {
  font-size: 2.5rem;
}
.multiply {
  font-size: 1.7rem;
}
// button.num {
  // background-color: #eee;
// }
// button.decimal {
  //  background-color:cadetblue;
// }
button.equal-sign {
  background-color: var(--yellow);
}

button:active {
  border: 2px solid var(--gray);
}

.calculator {
  background-color: var(--black);
  padding: .25em .5em .5em .5em;
  border-radius: 1em;
  margin: 10px;
  min-width: 350px;
  width: 40%;
  max-width: 450px;

  .segments {
    margin: .25em;
    display: flex;
    height: 20px;

    .seg {
      font-family:Arial, Helvetica, sans-serif;
      font-size: .7rem;
      letter-spacing: .02rem;
      height: 100%;
      display: flex;
      align-items: center;
      background-color: var(--white);
      padding: .35em;
      color: var(--black);
      margin: 0 .25em 0 0;
      border-radius: .1em;
    }
  }

  .display-input {
    width: 100%;
    height: 70px;
    font-size: 2rem;
    text-align: right;
    padding: 0 .15em;
    background-color: var(--black);
    border: 0;
    border-bottom: 2px solid var(--gray);
  }

  .buttons {
    width: 100%;
    margin-top: 10px;
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    grid-row-gap: 10px;
    grid-column-gap: 10px;
    grid-auto-flow: dense;

    .operator {
      grid-column-start: 4;
    }
    .equal-sign {
      grid-column-start: 1;
      grid-column-end: -1;
    }

    .ac {
      color: var(--white);
    }
  }
}

.history {
  margin-top: .75em;
  list-style: none;
  padding: 0 1em;
  align-self: flex-start;
  font-family: Arial, Helvetica, sans-serif;
  li {
    margin-bottom: .45em;
    padding: 0 .5em .5em .5em;
    border-bottom: 1px solid var(--gray);
  }
  li:last-child {
    border: 0;
  }
}

</style>
