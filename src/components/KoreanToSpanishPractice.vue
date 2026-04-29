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
        <span v-if="userNumber !== null">{{ userNumber }}</span>
        <span v-else class="placeholder">Usa los botones de abajo...</span>
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

    <div class="phone-keypad">
      <div class="keypad-row">
        <button @click="addNumber(1)" class="neutral-button">1</button>
        <button @click="addNumber(2)" class="neutral-button">2</button>
        <button @click="addNumber(3)" class="neutral-button">3</button>
      </div>
      <div class="keypad-row">
        <button @click="addNumber(4)" class="neutral-button">4</button>
        <button @click="addNumber(5)" class="neutral-button">5</button>
        <button @click="addNumber(6)" class="neutral-button">6</button>
      </div>
      <div class="keypad-row">
        <button @click="addNumber(7)" class="neutral-button">7</button>
        <button @click="addNumber(8)" class="neutral-button">8</button>
        <button @click="addNumber(9)" class="neutral-button">9</button>
      </div>
      <div class="keypad-row">
        <button @click="clearInput" class="neutral-button btn-clear">C</button>
        <button @click="addNumber(0)" class="neutral-button">0</button>
        <button @click="deleteLast" class="neutral-button btn-clear">⌫</button>
      </div>
    </div>
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
        this.isCorrect = false;
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
    },
    clearInput() {
      this.userNumber = null;
      this.feedback = '';
    },
    addNumber(num) {
      if (this.userNumber === null) {
        this.userNumber = num;
      } else {
        // Limitar a 3 dígitos máximo
        if (this.userNumber.toString().length < 3) {
          this.userNumber = this.userNumber * 10 + num;
        }
      }
      this.feedback = '';
    },
    deleteLast() {
      if (this.userNumber !== null) {
        const str = this.userNumber.toString();
        if (str.length === 1) {
          this.userNumber = null;
        } else {
          this.userNumber = parseInt(str.slice(0, -1));
        }
      }
      this.feedback = '';
    }
  }
};
</script>

<style scoped>
/* Component-specific styles only */
.target-hangul {
  color: #35495e;
}

.placeholder {
  color: #adb5bd;
  font-size: 1rem;
  font-weight: normal;
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

.phone-keypad {
  margin-top: 20px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  max-width: 280px;
  margin-left: auto;
  margin-right: auto;
}

.keypad-row {
  display: flex;
  justify-content: center;
  gap: 10px;
}

.keypad-row .neutral-button {
  width: 70px;
  height: 60px;
  font-size: 1.3rem;
  font-weight: 600;
  border-radius: 8px;
}

.btn-clear {
  background: #f8d7da !important;
  color: #721c24 !important;
  border-color: #f5c6cb !important;
}

.btn-clear:hover {
  background: #f5c6cb !important;
}
</style>