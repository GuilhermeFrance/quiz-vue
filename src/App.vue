

<template>
  <ScoreBoard/>
<template v-if="this.question">
  <div>

    <h1 v-html="this.question"></h1>

     <template v-for="(answer, index) in this.answers" :key= "index">
      <input 
      :disabled="this.answerSubmitted"
      type="radio" 
      name="options" 
      :value="answer"
      v-model="this.chosen_answer"/>
      <label v-html="answer"></label><br/>
      </template>
   
    <button v-if="!this.answerSubmitted" @click="this.submitAnswer()" :disabled="this.answerSubmitted" class="send" type="button">Enviar resposta</button>

    <section v-if="this.answerSubmitted">
      <h4 v-if="this.chosen_answer == this.CorrectAnswer" >&#128640; Parabéns, certa resposta!</h4>
      <h4 v-else v-html="'&#10060; Desculpe, você escolheu a resposta errada! A correta é ' + this.CorrectAnswer "</h4>
      <button v-if="this.answerSubmitted" @click="getNewQuestion()" class="send" type="button">Próxima pergunta</button>
    </section>
  </div>  
  
</template>
</template>

<script>

import ScoreBoard from './components/ScoreBoard.vue'

export default {
  name: 'App',
  components: {
    ScoreBoard
  },

  data() {
    return {
      question: undefined,
      IncorrectAnswer: undefined,
      CorrectAnswer: undefined,
      chosen_answer: undefined,
      answerSubmitted: false
    }
  },
computed: {
  answers(){
  var answers = JSON.parse(JSON.stringify(this.IncorrectAnswer));
  answers.splice( Math.round(Math.random()* 4 ), 0, this.CorrectAnswer)
  console.log(answers)
  return answers;
  }
},
methods: {
  submitAnswer(){
    if (!this.chosen_answer){
      alert('Escolha uma das alternativas!')
    }else {
      this.answerSubmitted = true;
      if(this.chosen_answer == this.CorrectAnswer){
        alert('Você acertou!')
      }else{
        alert('Você errou! :(')
      }
    }
  },

  getNewQuestion() {

    this.answerSubmitted = false;
    this.chosen_answer = undefined;
    

    this.axios
      .get('https://opentdb.com/api.php?amount=1&category=18&difficulty=easy')
      .then((response) => {
        this.question = response.data.results[0].question;
        this.IncorrectAnswer = response.data.results[0].incorrect_answers;
        this.CorrectAnswer = response.data.results[0].correct_answer;
        console.log(response.data.results[0])
      })
  }
},
  created() {
    this.getNewQuestion()
  },
}


</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;
}

input[type='radio'] {
  margin: 12px 4px;
}

button.send {
  margin-top: 12px;
  height: 40px;
  min-width: 120px;
  padding: 0 16px;
  color: #fff;
  background-color: #1867c0;
  border: 1px solid #1867c0;
  cursor: pointer;
}
</style>
