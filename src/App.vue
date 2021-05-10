<template>
  <div class="container">
      <header-section score="0"></header-section>
      <div v-if="state == 1">
        <p>Press Start Button to begin</p>
        <button @click="beginGame">Start</button>
      </div>
      <div v-if="state == 2">
        <h3>Time Left</h3>
        <div class="progress">
          <div class="progress-bar" role="progressbar" :style="countUpTime" :aria-valuenow="progress_position" aria-valuemin="0" aria-valuemax="600"></div>
        </div>
        <h3>Questions Left</h3>
        <div class="progress">
          <div class="progress-bar" role="progressbar" :style="questionCountDown" :aria-valuenow="progress_position" aria-valuemin="0" :aria-valuemax="no_of_questions"></div>
        </div>
        <div class="row center">
          <div class="col-4 p-3 m-3" align="center" style="background-color: #aba">
            <h3 class="display-2">{{ aElement }}</h3>
          </div>
          <div class="col-2 p-3 m-3" align="center" style="background-color: #faf">
            <h3 class="display-2">{{ opElement }}</h3>
          </div>
          <div class="col-4 p-3 m-3" align="center" style="background-color: #bab">
            <h3 class="display-2">{{ bElement }}</h3>
          </div>
        </div>
        <div class="row">
          <div class="col">Your Answer: <span class="btn-primary">{{enteredAnswer}}</span></div>
          <div class="col">Time left: <span class="btn-primary">{{timer}}</span></div>
        </div>
      </div>
    </div>
</template>

<script>
import HeaderSection from './components/HeaderSection.vue'

export default {
  name: 'App',
  components: {
    HeaderSection
  },
  created() {
    window.addEventListener('keypress', this.handleKeyboard);
  },
  destroyed() {
    window.removeEventListener('keypress', this.handleKeyboard)
  },
  mounted() {
    this.provided_answer = [];

    // window.addEventListener("keypress", function(e) {
    // });

    /**
     * Required
     * Show questions list
     * 10 
     * pos = 1
     * = 10%
     */
    //setup min and max

    
    //count number of questions - overload + and - to make these more likely to occur
    const operations = ['+', '+', '+', '-', '-', '-', '*', '/'];
    for(var qNo = 0; qNo < 10; qNo++) {
      //create questions 10
      let opPos = this.getRndInteger(0, operations.length - 1);
      let a = this.getRndInteger(this.min_number, this.max_number);
      let b = this.getRndInteger(this.min_number, this.max_number);
      let operation = operations[opPos];
      let ans = 0;
      if(operation =='+')
        ans = a + b;
      else if(operation =='-')
        ans = a - b;
      else if(operation =='*')
        ans = a * b;
      else 
        ans = a / b;
      let question = {a: a, op: operations[opPos], b: b, ans: ans};
      this.questions.push(question);
    }
    //
    
    //this.countUpTimer();
  },

  data() {
    return {
      provided_answer: [],
      min_number: 1,
      max_number: 6,
      no_of_questions: 10,
      progress_position: 25,
      timer: 600,
      questions: [],
      question_no: 1,
      state: 1,
      correct_questions: 1,
    };
  },

  methods: {
    handleKeyboard(e) {
      var localthis = this;
      if(e.keyCode === 13) {
        console.log('Enter pressed - convert value and check correct');
        var question;
        if(this.questions.length > 0 && this.question_no < this.questions.length) {
          question = this.questions[this.question_no - 1];
        }
        console.log(question);
        if(parseFloat(this.provided_answer) == parseFloat(question.ans)) {
          this.correct_questions += 1;
        } else {
          console.log('Failed question: ', localthis.provided_answer, ' - ', question.ans);
        }
        this.question_no += 1;
        this.provided_answer = '';
      } else {
        //const localthis = this;
        //add numbers to string until enter pressed
        const ans = String.fromCharCode(e.keyCode);
        if(ans != undefined && Array.isArray(this.provided_answer))
          localthis.provided_answer.push(ans);
        else {
          localthis.provided_answer = [];
          console.log("Ans: ", this.provided_answer);
          localthis.provided_answer.push(ans);
          //this.provided_answer.push(ans);
        }
          

        //console.log("Answer string: ", this.provided_answer.join(""));
      }
      //console.log(String.fromCharCode(e.keyCode));

    },
    countDownTimer() {
      if (this.timer > 0) {
        setTimeout(()=>{
          this.timer -= 1;
          this.countDownTimer();
        }, 1000);
      }
    },
    // countUpTimer() {
    //   if (this.timer < this.maxAllowedTime) {
    //     setTimeout(()=>{
    //       this.timer -= 1;
    //       this.countdownTimer();
    //     }, 1000);
    //   }
    // },
    getRndInteger(min, max) {
      return Math.floor(Math.random() * (max - min) ) + min;
    },
    gQ(qno, questions) {
      let question = {};
      console.log('fuck');
      console.log('QN;:', qno);
      console.log(questions);
      if(questions.length > 0 && qno < questions.length) {
        question = questions[qno - 1];
      }
      return question;
    },
    getQuestion(qno) {
      let question = {};
      if(this.questions.length > 0 && qno < this.questions.length) {
        question = this.questions[qno - 1];
      }
      return question;
    },
    beginGame() {
      this.countDownTimer();
      this.state = 2;
    }
  },

  computed: {
    countUpTime() {
      //console.log('answer: ',"width: "+(this.timer / 6)+"%");
      return "width: "+(this.timer / 6)+"%";
    },
    enteredAnswer() {
      if (this.provided_answer.length > 0)
        return this.provided_answer.join("");
      return '';
    },
    questionCountDown() {
      return "width: "+(this.question_no * 10)+"%";
    },
    aElement() {
      if(this.question_no > 0 && this.question_no != this.questions.length) {
        const question = this.getQuestion(this.question_no);
        if(question.a !== undefined)
          return question.a;
      }
      return "";
    },
    opElement() {
      if(this.question_no > 0 && this.question_no != this.questions.length) {
        const question = this.getQuestion(this.question_no);
        if(question.a !== undefined)
          return question.op;
      }
      return "";
    },
    bElement() {
      if(this.question_no > 0 && this.question_no != this.questions.length) {
        const question = this.getQuestion(this.question_no);
        if(question.a !== undefined)
          return question.b;
      }

      return "";
    },

  }
}
</script>
