<template>
  <div class="korean-quiz">
    <div class="card-header">
        <h1>Sino-Korean Quiz</h1>
        <button @click="switchMode" class="icon-button">
            <img src="@/assets/switch_arrow.svg" alt="Switch Practice" />
        </button>
    </div>
    
    <div class="challenge-box">
      <p>¿Cómo se escribe esto en hangul?</p>
      <div class="target-number">{{ targetNumber }}</div>
    </div>

    <div class="answer-section">
      <div class="input-display">
        <span v-if="userSelection">{{ userSelection }}</span>
        <span v-else class="placeholder">Selecciona los números de abajo...</span>
      </div>
      
      <div class="controls">
        <button @click="checkAnswer" class="submit-button">Corregir</button>
        <button @click="clearInput" class="clear-button">Limpiar</button>
        <button @click="generateNewChallenge" class="secondary-button">Nuevo Número</button>
      </div>
    </div>  

    <transition name="fade">
      <p v-if="feedback" :class="['feedback', isCorrect ? 'success' : 'error']">
        {{ feedback }}
      </p>
    </transition>

    <div class="divider"></div>

    <div class="keypad">
      <div class="key-group">
        <h4>Unidades (1 - 9)</h4>
        <div class="button-grid">
          <button 
            v-for="char in unitsButtons" 
            :key="char" 
            @click="addChar(char)"
            class="neutral-button"
          >
            {{ char }}
          </button>
        </div>
      </div>

      <div class="key-group">
        <h4>Posiciones</h4>
        <div class="button-grid">
          <button 
            v-for="pos in positionButtons" 
            :key="pos" 
            @click="addChar(pos)"
            class="neutral-button"
          >
            {{ pos }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'KoreanQuiz',
  emits: ['switch-mode'],
  data() {
    return {
      targetNumber: 0,
      userSelection: '',
      feedback: '',
      isCorrect: false,
      unitsButtons: ['일', '이', '삼', '사', '오', '육', '칠', '팔', '구'],
      positionButtons: ['십', '백', '천'],
      unitsRef: ['', '일', '이', '삼', '사', '오', '육', '칠', '팔', '구'],
      posRef: ['', '십', '백', '천', '만']
    };
  },
  mounted() {
    this.generateNewChallenge();
  },
  methods: {
    generateNewChallenge() {
      this.targetNumber = Math.floor(Math.random() * 10000);
      this.userSelection = '';
      this.feedback = '';
      this.isCorrect = false;
    },
    addChar(char) {
      this.userSelection += char;
      this.feedback = ''; 
    },
    clearInput() {
      this.userSelection = '';
      this.feedback = '';
    },
    getCorrectHangul(num) {
      if (num === 0) return '영';
      let numStr = num.toString();
      let res = '';
      let len = numStr.length;

      for (let i = 0; i < len; i++) {
        let digit = parseInt(numStr[i]);
        let pos = len - i - 1;
        if (digit !== 0) {
          // Sino-Korean logic: don't say "one-ten", just "ten"
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
      if (!this.userSelection) {
        this.feedback = " Por favor, ingresa tu respuesta antes de corregir.";
        return;
      }
      
      const correct = this.getCorrectHangul(this.targetNumber);
      if (this.userSelection === correct) {
        this.feedback = "¡Correcto! ¡참 잘했어요!";
        this.isCorrect = true;
      } else {
        this.feedback = `Incorrecto. Respuesta correcta: ${correct}`;
        this.isCorrect = false;
      }
    },
    switchMode() {
      this.$emit('switch-mode');
    }
  }
};
</script>

<style scoped>
/* Component-specific styles only */
.target-number {
  color: #35495e;
}

.placeholder {
  color: #adb5bd;
  font-size: 1rem;
  font-weight: normal;
}

.button-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
  margin-top: 10px;
}

.pos-btn {
  background: #f1fdf7;
  color: #2c3e50;
  font-weight: bold;
}

h4 {
  margin: 0;
  color: #6c757d;
  text-transform: uppercase;
  font-size: 0.8rem;
  letter-spacing: 1px;
}
</style>