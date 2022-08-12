<template>
  <div class="container">

    <!-- Add word to list form -->
    <div class="add_form">
    <form @submit.prevent="addItem" action="">
      <input v-model="inputWord" class="wordInput" type="text" placeholder="word...">
      <input v-model="inputTranslation" class="transInput" type="text" placeholder="translation...">
      <button>Add word</button>
    </form>
    <button @click="createTest">{{ ulVisible ? 'Create test' : 'Show words'}}</button>
    </div>

    <!-- Words list -->
    <ul v-if="ulVisible">
      <li v-for="item in listItems"> 
      {{ item.word }} - {{ item.translation }}
      <button class="deleteBtn" @click="deleteItem(item)">X</button>
      </li>
    </ul>

    <!-- Test list -->
    <ul v-if="ulVisible == false">
      <li v-for="test in testItems">
        {{test.word}} - <input v-model="test.answer" type="text">
      </li>
      <button @click="checkAnswer">Check</button><p>{{ checkTest }}</p>
    </ul>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        listItems: [],
        testItems: [],
        inputWord: '',
        inputTranslation: '',
        checkTest: '',
        ulVisible: true
      }
    },
    
    mounted() {
      // Load list from local storage
      if(localStorage.getItem('listItems')) {
          this.listItems = JSON.parse(localStorage.getItem('listItems'))
      }
    },

    methods: {
      
      addItem() {
        let wordInList = false; 
        // check if word already in list
        this.listItems.forEach(item => {
          if (item.word.toLowerCase().replace( /\s/g, '') == this.inputWord.toLowerCase().replace( /\s/g, '')) {
            alert('word already in list')
            wordInList = true;
            this.inputWord = ''
            this.inputTranslation = ''
          }
        });
        // add item to words list
        if(wordInList == false) {
          let item = {word: this.inputWord, translation: this.inputTranslation}
          this.listItems.push(item)
          this.saveItem();
          this.inputWord = ''
          this.inputTranslation = ''
        }
      },
      // save list in local storage
      saveItem() {
        const parsed = JSON.stringify(this.listItems);
        localStorage.setItem('listItems', parsed);
      },
      // delete item from list
      deleteItem(item) {
        this.listItems = this.listItems.filter((i) => i !== item);
        this.saveItem()
      },

      getRandom(max) {
        return Math.floor(Math.random() * max);
      },

      createTest() {
        this.testItems = [];

        if(this.ulVisible === true) {
          for (let i = 0; i < 5; i++) {
            let n = this.getRandom(this.listItems.length);
            let item = {word: this.listItems[n].word, translation: this.listItems[n].translation, answer: ''};
            this.testItems.push(item)
          }
          this.ulVisible = false
        } else {
          this.ulVisible = true
        }
      },

      checkAnswer() {
        for(let i = 0; i < 5; i++) {
          if(this.testItems[i].translation.toLowerCase() == this.testItems[i].answer.toLowerCase()) {
            this.checkTest = "Correct"
          } else {
            this.checkTest = 'Something wrong'
            break;
          }
        }
      }
    }
  }
</script>

<style>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

.container {
  max-width: 600px;
  margin: auto;
}

.add_form {
  display: flex;
  justify-content: center;
}

ul {
  list-style-type: none;
  padding-left: 5px;
  border: 1px solid pink;
  border-radius: 5px;
}

input {
  margin: 5px;
  padding: 5px;
  border: 2px solid pink;
  border-radius: 5px;
}
input:focus {
  outline: none;
  border: 2px solid skyblue;
}
button {
  margin: 5px;
  padding: 5px;
  background: none;
  border: 2px solid pink;
  border-radius: 5px;
  cursor: pointer;
}
button:hover {
  border-color: skyblue;
}

.deleteBtn {
  padding: 1px 3px 0px 3px;
}
</style>