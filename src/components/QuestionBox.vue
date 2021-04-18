<template>
  <div>
    <b-jumbotron>
      <template #lead>
        {{ currentQuestion.question | correctQuote | correctApostophe }}
      </template>

      <hr class="my-4" />
      <b-list-group>
        <!-- <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="answer.id"
          @click="selectAnswer(index)"
          :class="[
            selectedIndex === index && answered === false ? 'selected' : '',
            correctIndex === index && answered === true ? 'correct' : '',
            correctIndex != index && answered === true ? 'incorrect' : '',
          ]"
          >{{ answer }}
        </b-list-group-item> -->
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          @click.prevent="selectAnswer(index)"
          :class="answerClass(index)"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>
      <hr class="my-4" />
      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered === true"
      >
        Submit
      </b-button>
      <b-button variant="success" href="#" @click="next"
        >Next question</b-button
      >
    </b-jumbotron>
  </div>
</template>

<style scoped>
.list-group-item:hover:not(.selected):not(.correct):not(.incorrect) {
  background-color: #e6e6e6;
  cursor: pointer;
}
.btn {
  margin: 0 5px;
}
.selected {
  background-color: #68bbe3;
}
.list-group-item.selected:hover {
  background-color: #68bbe3;
}
.correct {
  background-color: #67e667;
}
.incorrect {
  background-color: #ea5555;
}
</style>


<script>
import _ from "lodash";

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false,
    };
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.shuffleAnswers();
        this.answered = false;
      },
    },
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
     answerClass(index) {
      let answerClass = ''
      if (!this.answered && this.selectedIndex === index) {
        answerClass = 'selected'
      } else if (this.answered && this.correctIndex === index) {
        answerClass = 'correct'
      } else if (this.answered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        answerClass = 'incorrect'
      }
      return answerClass
    },
    submitAnswer() {
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
    },
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    },
  },
  filters: {
    correctQuote(question) {
      return question.replace(/&quot;/g, '"');
    },
    correctApostophe(question) {
      return question.replace(/&#039;s/g, "'s");
    },
  },
};
</script>
