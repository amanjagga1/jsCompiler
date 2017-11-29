<template>
  <div id="app">
      <app-header></app-header>
      <div id="home">
        <div id ="console"></div>
        <!--app-console v-for="data in consoleData" :data="data"></app-console-->
        <div class="input">
          <textarea id="textInput" :rows="textAreaRows" autofocus="true" @keydown="handleData" v-model="inputValue"></textarea>
        </div>
    </div>
  </div>
</template>

<script>
import appHeader from './Header.vue'
import appConsole from './Console.vue'
import Vue from 'vue'
export default {
  components: {appHeader, appConsole},
  data () {
    return {
      textAreaRows: 1,
      consoleHistory: [],
      historyIndex : 0,
      inputValue: null,
      context : {}
    }
  },
  methods: {
    handleData(event) {
      //Arrow keys for history
      if (event.key == 'ArrowUp' && this.historyIndex - 1 >= 0) {
          this.historyIndex -= 1;
          this.inputValue = this.consoleHistory[this.historyIndex];
          
      } else if (event.key == 'ArrowDown' && this.historyIndex + 1 < this.consoleHistory.length) {
          this.historyIndex += 1;
          this.inputValue = this.consoleHistory[this.historyIndex];
      } else if ( event.key == 'Enter' && event.shiftKey) {
        this.textAreaRows += 1;
      } else if (event.key == 'Enter') {
        this.evaluateData(event);
      }
    },
    //Evaluate Data on Enter
    evaluateData(event) {
      event.preventDefault();
      if(this.inputValue) {
        let result = null;
        let warning = false;
          try {
            result = "" + eval.call(this.context, this.inputValue);
          }
          catch(e) {
            result = "" + e;
            warning = true;
          }
        this.pushToConsoleHistory(this.inputValue);
        this.mountToConsole({
          query: this.inputValue,
          result : result,
          warning: warning
        });
        this.inputValue = "";  
      }
    },

    //Add to Console History
    pushToConsoleHistory(value) {
      let index = this.consoleHistory.indexOf(value);
      if (index > -1)
        this.consoleHistory.splice(index, 1);
      this.consoleHistory.push(value);
    },

    //Mount the output component
    mountToConsole(propsData) {
      const container = document.querySelector('#console')
      const containerEntry = document.createElement('div')
      container.appendChild(containerEntry);
      
      const consoleComponent = Vue.extend(appConsole);
       const mounted = new consoleComponent({
        propsData : propsData
      }).$mount(containerEntry);

      window.scrollTo(0,document.body.scrollHeight);
    }
  },
  watch: {
    inputValue: function(value) {
        if (value) {
          this.textAreaRows = value.split("\n").length;
        }
    },
    //Reset the histroyIndex with new inputs
    consoleHistory: function(value) {
      this.historyIndex = value.length;
    }
  }
}
</script>

<style lang="scss">
  @import '~bulma/bulma';
  #home {
    margin-left: 10px;
  }
  .input {
    background-image: url('./assets/arrow.svg');
    background-repeat: no-repeat;
    background-size: 20px;
    border:none;
    height:auto;
    padding-bottom: 5px;
    #textInput {
      border: none;
      width: 100%;
      line-height: 25px;
      font-size: 16px;
      padding-left: 5px;
      margin-left: 10px;
      z-index: 2;
    }
    #textInput:focus {
      outline: 0;
    }
  }
  #console div{
    white-space: pre-wrap;
  }
   
</style>
