<template>
  <div id="app">
    <div class="content">
      <transition name="fade" mode="out-in">
        <KoreanToSpanishPractice v-if="currentMode === 'build'" @switch-mode="toggleMode" />
        <SpanishToKoreanPractice v-else @switch-mode="toggleMode" />
      </transition>
    </div>
  </div>
</template>

<script>
import KoreanToSpanishPractice from './components/KoreanToSpanishPractice.vue';
import SpanishToKoreanPractice from './components/SpanishToKoreanPractice.vue';
export default {
  name: 'App',
  components: {
    KoreanToSpanishPractice,
    SpanishToKoreanPractice
  },
  data() {
    return {
      currentMode: 'build' 
    };
  },
  methods: {
    toggleMode() {
      this.currentMode = this.currentMode === 'build' ? 'read' : 'build';
    }
  }
};
</script>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
