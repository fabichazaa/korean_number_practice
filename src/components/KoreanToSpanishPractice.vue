<template>
  <div class="korean-quiz">
    <div class="card-header">
        <h1>Sino-Korean Quiz</h1>
        <button @click="switchMode" class="icon-button">
            <img src="@/assets/switch_arrow.svg" alt="Switch Practice" />
        </button>
    </div>
    
    <div class="challenge-box">
      <p>¿Qué número es este?</p>
      <div class="target-hangul">{{ targetHangul }}</div>
    </div>

    <div class="answer-section">
      <div class="input-display">
        <input 
          type="number" 
          v-model.number="userNumber" 
          placeholder="Escribí el número aquí..."
          @keyup.enter="checkAnswer"
          ref="numberInput"
        />
      </div>
      
      <div class="controls">
        <button @click="checkAnswer" class="submit-button">Corregir</button>
        <button @click="generateNewChallenge" class="secondary-button">Nuevo Número</button>
      </div>
    </div>

    <transition name="fade">
      <p v-if="feedback" :class="['feedback', isCorrect ? 'success' : 'error']">
        {{ feedback }}
      </p>
    </transition>
  </div>
</template>

<script>
export default {
  name: 'ReverseKoreanQuiz',
  data() {
    return {
      targetNumber: 0,
      targetHangul: '',
      userNumber: null,
      feedback: '',
      isCorrect: false,
      streak: 0,
      unitsRef: ['', '일', '이', '삼', '사', '오', '육', '칠', '팔', '구'],
      posRef: ['', '십', '백', '천', '만']
    };
  },
  mounted() {
    this.generateNewChallenge();
  },
  methods: {
    generateNewChallenge() {
      this.targetNumber = Math.floor(Math.random() * 100) + 1;
      this.targetHangul = this.getHangulString(this.targetNumber);
      this.userNumber = null;
      this.feedback = '';
      this.isCorrect = false;

      // Auto-focus the input
      this.$nextTick(() => {
        if (this.$refs.numberInput) this.$refs.numberInput.focus();
      });
    },
    getHangulString(num) {
      if (num === 0) return '영';
      let numStr = num.toString();
      let res = '';
      let len = numStr.length;
      for (let i = 0; i < len; i++) {
        let digit = parseInt(numStr[i]);
        let pos = len - i - 1;
        if (digit !== 0) {
          if (digit === 1 && pos > 0 && pos < 4) {
            res += this.posRef[pos];
          } else {
            res += this.unitsRef[digit] + this.posRef[pos];
          }
        }
      }
      return res;
    },
    checkAnswer() {
      if (this.userNumber === null) {
        this.feedback = "Por favor, ingresa un número.";
        return;
      }

      if (this.userNumber === this.targetNumber) {
        this.feedback = "¡Correcto! ¡참 잘했어요!";
        this.isCorrect = true;
        this.streak++;
      } else {
        this.feedback = `Incorrecto. ${this.targetHangul} es ${this.targetNumber}`;
        this.isCorrect = false;
        this.streak = 0;
      }
    },
    resetStreak() {
      this.streak = 0;
      this.generateNewChallenge();
    },
    switchMode() {
      this.$emit('switch-mode');
    }
  }
};
</script>

<style scoped>
/* Component-specific styles only */
.target-hangul {
  color: #35495e;
}

.btn-clear { 
  background: #6c757d; 
  color: white; 
  font-size: 0.7rem; 
  padding: 5px 10px !important; 
}

.btn-clear:hover {
  background: #5a6268;
}

h4 {
  margin: 0;
  color: #6c757d;
  text-transform: uppercase;
  font-size: 0.8rem;
  letter-spacing: 1px;
}
</style>