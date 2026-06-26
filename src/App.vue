<template>
  <main class="divination-wrapper">
    <header class="game-header">
      <h1 class="main-title">六十甲子籤</h1>
      <p class="subtitle">沉澱心靈，默念所求之事，點擊抽籤。</p>
    </header>

    <div class="divination-scene">
      <div :class="['shaker', { 'is-shaking': isShaking }]">
        <div class="cylinder">
          <div class="inner-sticks">
            <span v-for="i in 5" :key="i" class="deco-stick"></span>
          </div>
        </div>
      </div>

      <transition name="stick-drop">
        <div v-if="isDropping" class="falling-stick">
          <span class="stick-label">{{ currentFortune.id.split('【')[0] }}</span>
        </div>
      </transition>
    </div>

    <div class="action-zone">
      <button 
        class="draw-button" 
        :disabled="isShaking || isDropping"
        @click="handleDraw"
        aria-live="polite"
      >
        <span v-if="isShaking">求籤中...</span>
        <span v-else-if="isDropping">籤詩落地...</span>
        <span v-else>祈福抽籤</span>
      </button>
    </div>

    <section 
      id="result-box" 
      class="result-card" 
      v-if="showResult"
      aria-live="assertive"
    >
      <div class="calligraphy-font">
        <div class="fortune-meta">
          <span class="fortune-id">{{ currentFortune.id }}</span>
          <span class="fortune-bagua">{{ currentFortune.title }}</span>
        </div>
        <hr class="flower-divider" />
        <div class="poem-body" v-html="currentFortune.poem"></div>
        <hr class="flower-divider" />
        <div class="meaning-box">
          <strong>【解曰】：</strong> {{ currentFortune.meaning }}
        </div>
      </div>
      <button class="reset-btn" @click="resetGame">重新求籤</button>
    </section>
  </main>
</template>

<script setup>
import { ref } from 'vue';

const fortuneData = [
  { 
    id: "第一籤【甲子】", 
    title: "乾為天．大吉", 
    poem: "日出便見風雲散<br>光明清淨照世間<br>一向前途通大道<br>萬事清吉保平安",
    meaning: "前途光明，即便目前有阻礙，也會迅速消散，適合大膽進取。"
  },
  { 
    id: "第十一籤【乙酉】", 
    title: "地水師．上吉", 
    poem: "靈雞漸漸見分明<br>凡事且看子丑寅<br>雲開月出照天下<br>郎君即便見太平",
    meaning: "苦盡甘來，現在的磨練是為了之後的成功，好消息就在不遠處。"
  },
  { 
    id: "第二十二籤【丁未】", 
    title: "天水訟．中吉", 
    poem: "太公家業八十成<br>月出光輝四海明<br>眼前料得皆無事<br>合家和樂保安寧",
    meaning: "大器晚成，不要急於一時的成功，守住本心，家和萬事興。"
  },
  { 
    id: "第四十籤【庚午】", 
    title: "水澤節．平吉", 
    poem: "平生富貴成祿位<br>君家門戶定光輝<br>此中必定無損失<br>夫妻百歲喜相隨",
    meaning: "生活平穩，雖然沒有橫財，但平安即是福，感情與家庭非常美滿。"
  }
];

const isShaking = ref(false);
const isDropping = ref(false);
const showResult = ref(false);
const currentFortune = ref({});

const handleDraw = () => {
  showResult.value = false;
  isShaking.value = true;
  
  setTimeout(() => {
    isShaking.value = false;
    isDropping.value = true;
    
    const randomIndex = Math.floor(Math.random() * fortuneData.length);
    currentFortune.value = fortuneData[randomIndex];

    setTimeout(() => {
      isDropping.value = false;
      showResult.value = true;
    }, 800);
  }, 1000);
};

const resetGame = () => {
  showResult.value = false;
  isDropping.value = false;
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Cause:wght@100..900&family=Noto+Serif:ital,wght@0,100..900;1,100..900&family=Nunito:ital,wght@0,200..1000;1,200..1000&display=swap');

.divination-wrapper {
  background: linear-gradient(135deg, #F7E9DD 0%, #FFFFFF 50%, #F1E0D0 100%);
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 32px;
  font-family: 'Nunito', sans-serif;
  color: #222222;
}

.game-header {
  text-align: center;
  margin-bottom: 32px;
}

.main-title {
  font-family: 'Cause', 'Nunito', sans-serif;
  font-size: 40px;
  margin: 0;
  color: #222222;
  text-shadow: 2px 2px 4px rgba(255, 228, 225, 0.8);
}

.subtitle {
  font-size: 18px;
  opacity: 0.8;
  color: #4A3E65;
}

.divination-scene {
  position: relative;
  height: 200px;
  width: 200px;
  display: flex;
  justify-content: center;
  align-items: flex-end;
  margin-bottom: 32px;
}

.cylinder {
  width: 70px;
  height: 110px;
  background: linear-gradient(to bottom, #FF8C78, #FF6F61);
  border: 4px solid #fff;
  border-radius: 10px 10px 5px 5px;
  position: relative;
  box-shadow: 0 10px 20px rgba(0,0,0,0.15);
}

.inner-sticks {
  position: absolute;
  top: -30px;
  left: 10px;
  display: flex;
  gap: 4px;
}

.deco-stick {
  width: 6px;
  height: 50px;
  background-color: #F7E9DD;
  border-radius: 3px;
  transform-origin: bottom;
}

.shaker.is-shaking {
  animation: shake-cylinder 0.3s infinite;
}

@keyframes shake-cylinder {
  0%, 100% { transform: rotate(0deg); }
  25% { transform: rotate(-15deg) translateX(-5px); }
  75% { transform: rotate(15deg) translateX(5px); }
}

.falling-stick {
  position: absolute;
  width: 12px;
  height: 80px;
  background-color: #fff;
  border: 2px solid #FF6F61;
  border-radius: 5px;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 10;
  box-shadow: 0 4px 10px rgba(0,0,0,0.15);
}

.stick-label {
  writing-mode: vertical-rl;
  font-size: 10px;
  color: #222222;
}

.stick-drop-enter-active {
  animation: drop-out 0.8s ease-out forwards;
}

@keyframes drop-out {
  0% { transform: translateY(-50px) rotate(0deg); opacity: 1; }
  30% { transform: translateY(-80px) rotate(45deg); opacity: 1; }
  100% { transform: translateY(120px) rotate(90deg); opacity: 1; }
}

.draw-button {
  background: linear-gradient(45deg, #FF8C78, #FF6F61);
  color: #fff;
  border: none;
  padding: 16px 48px;
  font-size: 20px;
  font-weight: 800;
  border-radius: 50px;
  cursor: pointer;
  box-shadow: 0 5px 15px rgba(247, 233, 221, 0.6);
  transition: all 0.3s;
}

.draw-button:disabled {
  background: #CBD5E1;
  cursor: not-allowed;
  box-shadow: none;
}

.result-card {
  background: rgba(255, 255, 255, 0.98);
  margin-top: 32px;
  padding: 40px;
  border-radius: 15px;
  width: 100%;
  max-width: 450px;
  box-shadow: 0 15px 35px rgba(0,0,0,0.1);
  border: 1px solid #FF6F61;
}

.fortune-meta {
  display: flex;
  justify-content: space-between;
  font-size: 19px;
  color: #4A3E65;
  margin-bottom: 8px;
}

.flower-divider {
  border: 0;
  border-top: 2px dashed #F7E9DD;
  margin: 16px 0;
}

.poem-body {
  font-size: 29px;
  line-height: 45px;
  letter-spacing: 4px;
  color: #222222;
}

.meaning-box {
  font-size: 16px;
  background-color: #F8FBFF;
  padding: 16px;
  border-radius: 8px;
  margin-top: 16px;
  text-align: left;
  color: #4A3E65;
}

.reset-btn {
  margin-top: 24px;
  padding: 8px 24px;
  background: none;
  border: 1px solid #FF6F61;
  color: #FF6F61;
  border-radius: 20px;
  cursor: pointer;
}
</style>