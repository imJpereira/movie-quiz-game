<template>
  <ScoreBoard :playerPoints="this.playerPoints" :computerPoints="this.computerPoints" />
  <template v-if="this.question">
    <h1 v-html="this.question"></h1>
    <div class="options">
      <template v-for="answer, index in this.alternatives" :key="index">
        <input 
        type="radio" 
        :value="answer" 
        name="options" 
        v-model="this.chosenAnswer"
        :disabled="this.answerSent"
        >
        <label v-html="answer"></label><br>
      </template>
    </div>
    <template v-if="!this.answerSent">
      <div class="button-container">
        <button @click="this.submitAnswer(); this.countPoint(); this.disabledAfter3Sec()">Submit</button>
      </div>
    </template>
    <template v-else>
      <template v-if="this.chosenAnswer == this.correctAnswer">
        <p v-html="'&#10004;&#65039; Correct answer! '"></p>
      </template>
      <template v-else>
        <p v-html="'&#x274C; Wrong answer. The correct answer is ' + this.correctAnswer + '.'"></p>
      </template>
      <div class="button-container">
        <button 
        @click="this.newQuestion(); this.cleanData(); this.disabledButton = true" 
        :disabled="this.disabledButton"
        :class="{disabled: disabledButton}"
        >Next question</button>          
      </div>
    </template>
  </template>
</template>

<script>

import ScoreBoard from "./components/ScoreBoard.vue";

export default {
    name: "App",
    components: {
      ScoreBoard
    },
    
    data() {
      return {
        question: undefined,
        correctAnswer: undefined,
        incorrectAnswers: [],
        chosenAnswer: undefined,
        answerSent: false,
        playerPoints: 0,
        computerPoints: 0,
        loading: false,
        disabledButton: true
      }
    },

    computed: {
      alternatives() {
        const answers = [...this.incorrectAnswers];
        const randIndex = Math.round(Math.random() * answers.length);
        answers.splice(randIndex, 0, this.correctAnswer);
        return answers;
      }
    },

    methods: {
      newQuestion() {
        this.axios.get("https://opentdb.com/api.php?amount=1&category=11")
        .then(response => {
          this.question = response.data.results[0].question;
          this.correctAnswer = response.data.results[0].correct_answer;
          this.incorrectAnswers = response.data.results[0].incorrect_answers;
          this.loading = false;
        })
        .catch(() => {
          console.log('ERRO')
          this.loading = true;
          this.retryFetchQuestion();
        });
      },

      submitAnswer() {
        if (!this.chosenAnswer) {
          alert("Pick one alternative");
          return;
        }
        this.answerSent = true;
      },

      retryFetchQuestion() {
        setTimeout(() => {
          this.newQuestion();
        }, 2000);
      },

      disabledAfter3Sec() {
        setTimeout(() => {
          this.disabledButton = false;
        }, 3000);
      },

      countPoint() {
        if (this.chosenAnswer == this.correctAnswer) {
          this.playerPoints++;
          return;
        }

        this.computerPoints++;
      },

      cleanData() {
        this.answerSent = false;
        this.chosenAnswer = undefined;
      }

    },

    created() {
      this.newQuestion();
    }

  };

</script>

<style lang="scss">
  
  h1 {
    text-align: center;
  } 

  .options {
    font-size: 20px;
  }

  .button-container{
    display: flex;
    justify-content: center;
    margin: 20px 0;

    button {
      background-color: rgb(59, 59, 255);
      color: #fff;
      border: none;
      padding: 15px;
      min-width: 100px;
      cursor: pointer;
    } 

    .disabled {
      background-color: rgb(148, 148, 255);
      cursor: default;
    }
  }
  
</style>
