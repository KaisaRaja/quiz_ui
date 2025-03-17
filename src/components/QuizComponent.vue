<template>
  <div v-if="!quizFinished">
    <div class="question" v-if="currentQuestionIndex < questions.length">
      <p>{{ currentQuestion.question }}</p>
      <div class="answers-container">
        <button v-for="answer in currentQuestion.answers" :key="answer"
                @click="selectAnswer(answer)" :class="{'selected': selectedAnswer === answer}">{{ answer }}
        </button>

        <div v-if="feedbackMessage" :class="{'correct': feedbackMessage === 'Õige vastus!', 'incorrect': feedbackMessage === 'Vale vastus!'}">
          {{ feedbackMessage }}
        </div>
      </div>
    </div>

    <div class="next-container">
      <button v-if="showNextButton" @click="nextQuestion" class="next-button">Järgmine küsimus:</button>
    </div>
  </div>

  <div v-if="quizFinished">
    <h2>Sinu tulemus: {{ score }} / {{ questions.length }}</h2>
    <table>
      <thead>
      <tr>
        <th>Küsimus</th>
        <th>Sinu vastus:</th>
        <th>Õige vastus:</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(answer, index) in userAnswers" :key="index">
        <td>{{ answer.question }}</td>
        <td>{{ answer.userAnswer }}</td>
        <td>{{ answer.correctAnswer }}</td>
      </tr>
      </tbody>
    </table>
    <div class="text" v-if="score === 0">Seekord ei läinud hästi, kuid ära heida meelt! Proovi uuesti ja saad parema tulemuse!
    </div>
    <div class="text" v-if="score === 1">Tubli! Vastasid ühe küsimuse õigesti. Harjutamine teeb meistriks - proovi veel!
    </div>
    <div class="text" v-if="score > 1 && score < questions.length">
      Väga hea! Vastasid {{ questions.length }}-st küsimusest õigesti {{ score }}. Kas proovid uuesti, et saavutada täiuslik tulemus?
    </div>
    <div class="text" v-if="score === questions.length">
      Suurepärane! Vastasid kõikidele küsimustele õigesti! Oled tõeline asjatundja!
    </div>
    <button v-if="quizFinished" @click="restartQuiz">Alusta uuesti!</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      allQuestions: [
        {
          question: "Mis on Eesti pealinn?",
          answers: ["Tallinn", "Tartu", "Narva"],
          correctAnswer: "Tallinn"
        },
        {
          question: "Millal on Eesti Vabariigi aastapäev?",
          answers: ["24.veebruar", "24.juuni", "24.detsember"],
          correctAnswer: "24.veebruar"
        },
        {
          question: "Mis on Eesti rahvuslill?",
          answers: ["Piibeleht", "Rukkilill", "Sinilill"],
          correctAnswer: "Rukkilill"
        },
        {
          question: "Millal on emakeelepäev?",
          answers: ["7.aprill", "22.juuni", "14.märts"],
          correctAnswer: "14.märts"
        },
        {
          question: "Kes on Eesti hümni autor?",
          answers: ["Friedrich Reinhold Kreutzwald", "Johann Voldemar Jannsen", "Gustav Ernesaks"],
          correctAnswer: "Johann Voldemar Jannsen"
        },
        {
          question: "Mitu maakonda on Eestis?",
          answers: ["15", "13", "9"],
          correctAnswer: "15"
        },
        {
          question: "Milline neist on Eesti saar?",
          answers: ["Gotland", "Bornholm", "Saaremaa"],
          correctAnswer: "Saaremaa"
        },
        {
          question: "Millises Eesti linnas asub Eesti Maaülikool?",
          answers: ["Tallinn", "Tartu", "Pärnu"],
          correctAnswer: "Tartu"
        }
      ],
      questions: [],
      currentQuestionIndex: 0,
      score: 0,
      userAnswers: [],
      showNextButton: false,
      quizFinished: false,
      selectedAnswer: null,
      feedbackMessage: "",
      answerSelected: false,
    };
  },
  computed: {
    currentQuestion() {
      return this.questions[this.currentQuestionIndex];
    }
  },
  methods: {
    shuffleQuestions() {
      let shuffled = [...this.allQuestions].sort(() => Math.random() - 0.5);
      this.questions = shuffled.slice(0, 5);
      this.currentQuestionIndex = 0;
      },
      selectAnswer(answer) {
        if (!this.answerSelected) {
          this.selectedAnswer = answer;
          this.answerSelected = true;
          this.showNextButton = true;
          this.showFeedback();
          if (this.currentQuestionIndex === 0) {
            this.$emit("first-answer");
          }
        }
    },
    showFeedback() {
      const currentQuestion = this.currentQuestion;
      const isCorrect = this.selectedAnswer === currentQuestion.correctAnswer;
      this.feedbackMessage = isCorrect ? 'Õige vastus!' : 'Vale vastus!';
      this.userAnswers = this.userAnswers.filter(item => item.question !== currentQuestion.question);
      this.userAnswers.push({
        question: currentQuestion.question,
        userAnswer: this.selectedAnswer,
        correctAnswer: currentQuestion.correctAnswer
      });
      if (isCorrect) {
        this.score = this.userAnswers.filter(item => item.userAnswer === item.correctAnswer).length;
      }
    },
    nextQuestion() {
      this.currentQuestionIndex++;
      this.selectedAnswer = null;
      this.answerSelected = false;
      this.feedbackMessage = "";
      if (this.currentQuestionIndex >= this.questions.length) {
        this.quizFinished = true;
      } else {
        this.showNextButton = false;
      }
    },
    restartQuiz() {
      if (this.quizFinished === true) {
        this.quizFinished = false;
        this.score = 0;
        this.currentQuestionIndex = 0;
        this.userAnswers = [];
        this.showNextButton = false;
        this.feedbackMessage = "";
        this.answerSelected = false;
        this.shuffleQuestions();
      }
    }
  },
  created() {
    this.shuffleQuestions();
  }
};
</script>

<style scoped>
.answers-container {
  display: inline-block;
  flex-direction: column;
  align-items: center;
  padding: 10px 20px;
  border-radius: 20px;
}

button {
  font-size: 18px;
  width: 200px;
  padding: 10px;
  border-radius: 20px;
  margin: 10px;
  cursor: pointer;
  text-align: center;
  display: block;
}

button.selected {
  color: white;
  background-color: black;
}

button:hover {
  background-color: grey;
}

.next-container {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

.next-button {
  width: 200px;
}

.correct {
  color: green;
  font-size: 24px;
  font-weight: bold;
  margin-top: 10px;
}

.incorrect {
  color: red;
  font-size: 24px;
  font-weight: bold;
  margin-top: 10px;
}

table {
  margin-top: 20px;
  width: 100%;
  border-collapse: collapse;
  background-color: white;
}

table th,
table td {
  border: 1px solid #ddd;
  padding: 20px;
  text-align: left;
}

h1 {
  font-size: 48px;
}

h2 {
  font-size: 32px;
  color: red;
  background-color: #efebea;
  border-radius: 20px;
}

.question {
  margin-bottom: 20px;
  font-size: 34px;
  align-items: center;
  background-color: #fbfbfb;
  padding: 10px 20px;
  border-radius: 20px;
  display: inline-block;
}

.text {
  margin-top: 20px;
  font-size: 22px;
  font-weight: bold;
  color: #000000;
  background-color: #efebea;
  padding: 10px 20px;
  border-radius: 20px;
  display: inline-block;
}
</style>
