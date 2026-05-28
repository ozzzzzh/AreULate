<template>
  <view class="page">
    <!-- 第一步：入口 - 倒苦水 -->
    <view v-if="step === 1" class="step-one">
      <view class="weather-bg">
        <view v-if="weather.type === 'sunny'" class="sunny-bg">
          <view class="sun"></view>
        </view>
        <view v-if="weather.type === 'rainy'" class="rainy-bg">
          <view class="rain-drop" v-for="i in 20" :key="i" :style="getRainStyle(i)"></view>
        </view>
      </view>

      <view class="weather-info">
        <text class="weather-temp">{{ weather.temp }}°</text>
        <text class="weather-desc">{{ weather.desc }}</text>
      </view>

      <view class="center-area">
        <text class="main-title">迟到了么？</text>
        <view
          class="panic-button"
          :style="{ transform: `scale(${buttonScale})` }"
          @touchstart="onTouchStart"
        >
          <text class="panic-text">我是倒霉蛋</text>
        </view>
      </view>

      <text class="version">v{{ version }}</text>
    </view>

    <!-- 第二步：毒舌共鸣 -->
    <view v-if="step === 2" class="step-two">
      <view class="glitch-bg"></view>
      <view class="monster-face">
        <text class="monster-eye">◔‿◔</text>
      </view>
      <text class="toxic-line">{{ currentToxic }}</text>
      <view class="next-btn" @click="goStepThree">
        <text class="next-text">继续发疯 →</text>
      </view>
    </view>

    <!-- 第三步：疯狂摇晃 - 点击版 -->
    <view v-if="step === 3" class="step-three">
      <view class="shake-bg" :class="{ 'shake-it': isShaking }"></view>
      <text class="shake-title">老大！我们怎么办啊！天塌啦！</text>
      <view
        class="chaos-button"
        :style="{ transform: `scale(${shakeScale}) rotate(${rotateAngle}deg)` }"
        @touchstart="onShakeStart"
        @touchend="onShakeEnd"
      >
        <text class="chaos-emoji">{{ shakeEmoji }}</text>
      </view>
      <text class="shake-hint">疯狂点击！！</text>
      <view class="shit-rain" v-if="showShitRain">
        <text class="shit" v-for="i in 10" :key="i" :style="getShitStyle(i)">💩</text>
      </view>
    </view>

    <!-- 第四步：倒霉蛋卡片 -->
    <view v-if="step === 4" class="step-four">
      <view class="card-bg"></view>
      <view class="loser-card">
        <view class="card-header">
          <text class="card-label">今日限定</text>
          <text class="card-main-title">世界级倒霉蛋认证</text>
        </view>

        <view class="card-body">
          <text class="card-subtitle">恭喜你获得称号</text>
          <text class="card-name">{{ loserTitle }}</text>

          <view class="card-divider"></view>

          <text class="fortune-label">今日运势</text>
          <text class="fortune-text">忌：试图讲理</text>
          <text class="fortune-text">宜：保持呼吸</text>
          <text class="fortune-quote">"只要你活得够久，就能看到这个世界整出新花活"</text>
        </view>

        <view class="card-footer">
          <text class="card-date">{{ todayDate }}</text>
          <text class="card-seal">认证无效</text>
        </view>
      </view>

      <view class="exit-btn" @click="exitApp">
        <text class="exit-text">行了，老子继续回去演正常人了</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      version: '3.0.0',
      step: 1,
      buttonScale: 1,
      shrinkSpeed: 0.02,
      shrinkTimer: null,
      explodeTimer: null,
      weather: { type: 'sunny', temp: 26, desc: '晴天' },

      // 第二步
      toxicLines: [
        '真行，身体都抗议了，人还是得打卡。资本主义又赢一分。',
        '早上阳光这么好，按理该请假晒太阳，结果你在赶路，真幽默。',
        '迟到怎么了？地球不还是照样转？哦对，你的工资不转。',
        '又迟到了？没事，反正迟到扣的是钱，又不是命...等等，好像也差不多。',
        '恭喜你，今天又是为资本家打工的一天，而且还迟到了。',
        '别难过，迟到说明你至少还有不想去的自觉。'
      ],
      currentToxic: '',

      // 第三步
      shakeScale: 1,
      rotateAngle: 0,
      shakeEmoji: '🤪',
      shakeEmojis: ['🤪', '🥴', '😵', '🫠', '💀', '😠', '🤬', '😡'],
      isShaking: false,
      showShitRain: false,
      shakeCount: 0,
      shakeTarget: 30,

      // 第四步
      loserTitles: [
        '【西西弗斯中国区总代理】',
        '【草台班子一级护林员】',
        '【纯度100%的无辜受害者】',
        '【迟到界的非物质文化遗产】',
        '【打工魂燃烧殆尽者】',
        '【世界级倒霉蛋·至尊版】'
      ],
      loserTitle: ''
    }
  },
  computed: {
    todayDate() {
      const d = new Date()
      return `${d.getFullYear()}.${d.getMonth() + 1}.${d.getDate()}`
    }
  },
  onLoad() {
    this.loadWeather()
    this.startShrinking()
  },
  onUnload() {
    clearInterval(this.shrinkTimer)
    clearTimeout(this.explodeTimer)
  },
  methods: {
    loadWeather() {
      const types = [
        { type: 'sunny', temp: 32, desc: '该死的晴天' },
        { type: 'rainy', temp: 18, desc: '倒霉的雨天' }
      ]
      this.weather = types[Math.floor(Math.random() * types.length)]
    },
    startShrinking() {
      this.shrinkTimer = setInterval(() => {
        if (this.step !== 1) return
        if (this.buttonScale > 1) {
          this.buttonScale -= this.shrinkSpeed
          if (this.buttonScale < 1) this.buttonScale = 1
        }
        if (this.shrinkSpeed < 0.03) this.shrinkSpeed += 0.001
      }, 16)
    },
    onTouchStart() {
      if (this.step !== 1) return
      this.buttonScale += 0.35
      this.shrinkSpeed = Math.max(0.005, this.shrinkSpeed - 0.02)
      clearTimeout(this.explodeTimer)
      this.explodeTimer = setTimeout(() => {
        if (this.step === 1) this.goStepTwo()
      }, 500)
    },
    goStepTwo() {
      clearInterval(this.shrinkTimer)
      clearTimeout(this.explodeTimer)
      this.step = 2
      this.currentToxic = this.toxicLines[Math.floor(Math.random() * this.toxicLines.length)]
    },
    goStepThree() {
      this.step = 3
      this.shakeCount = 0
      this.shakeScale = 1
    },
    onShakeStart() {
      if (this.step !== 3) return
      this.isShaking = true
      this.shakeScale += 0.15
      this.rotateAngle = (Math.random() - 0.5) * 30
      this.shakeEmoji = this.shakeEmojis[Math.floor(Math.random() * this.shakeEmojis.length)]
      this.shakeCount++

      if (this.shakeCount > 10 && !this.showShitRain) {
        this.showShitRain = true
      }

      if (this.shakeCount >= this.shakeTarget) {
        setTimeout(() => this.goStepFour(), 300)
      }
    },
    onShakeEnd() {
      this.isShaking = false
      this.rotateAngle = 0
    },
    getShitStyle(i) {
      return {
        left: `${Math.random() * 100}%`,
        animationDelay: `${Math.random() * 2}s`,
        fontSize: `${20 + Math.random() * 20}px`
      }
    },
    getRainStyle(i) {
      return {
        left: `${Math.random() * 100}%`,
        animationDelay: `${Math.random() * 1}s`
      }
    },
    goStepFour() {
      this.step = 4
      this.loserTitle = this.loserTitles[Math.floor(Math.random() * this.loserTitles.length)]
    },
    exitApp() {
      // 模拟退出
      uni.showModal({
        title: '拜拜',
        content: '继续去演戏吧，倒霉蛋',
        showCancel: false,
        success: () => {
          this.step = 1
          this.buttonScale = 1
          this.shrinkSpeed = 0.02
          this.startShrinking()
        }
      })
    }
  }
}
</script>

<style scoped>
.page {
  min-height: 100vh;
  background: #f5f5f5;
}

/* ========== 第一步：入口 ========== */
.step-one {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: relative;
  overflow: hidden;
}

.weather-bg {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  pointer-events: none;
}

.weather-info {
  position: absolute;
  top: 50px;
  left: 30px;
}

.weather-temp {
  font-size: 36px;
  font-weight: 200;
  color: #333;
}

.weather-desc {
  display: block;
  font-size: 14px;
  color: #888;
  margin-top: 4px;
}

.sun {
  position: absolute;
  top: 60px;
  right: 30px;
  width: 70px;
  height: 70px;
  background: linear-gradient(135deg, #ffd93d 0%, #ff6b6b 100%);
  border-radius: 50%;
  box-shadow: 0 0 50px rgba(255, 217, 61, 0.5);
  animation: pulse-sun 3s ease-in-out infinite;
}

@keyframes pulse-sun {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.1); }
}

.rainy-bg {
  background: linear-gradient(180deg, #4a5568 0%, #2d3748 100%);
}

.rain-drop {
  position: absolute;
  top: 0;
  width: 2px;
  height: 15px;
  background: linear-gradient(180deg, transparent, rgba(255,255,255,0.5));
  animation: rain-fall 0.8s linear infinite;
}

@keyframes rain-fall {
  0% { transform: translateY(0); }
  100% { transform: translateY(100vh); }
}

.center-area {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.main-title {
  font-size: 28px;
  font-weight: 600;
  color: #333;
  margin-bottom: 50px;
}

.panic-button {
  width: 200px;
  height: 200px;
  border-radius: 50%;
  background: linear-gradient(145deg, #e74c3c, #8e44ad);
  box-shadow: 0 10px 40px rgba(231, 76, 60, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
  transition: transform 0.08s;
}

.panic-text {
  font-size: 22px;
  font-weight: 700;
  color: #fff;
  text-align: center;
  letter-spacing: 2px;
}

.version {
  position: absolute;
  bottom: 40px;
  font-size: 12px;
  color: #bbb;
}

/* ========== 第二步：毒舌共鸣 ========== */
.step-two {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 30px;
  background: #1a1a2e;
  position: relative;
}

.glitch-bg {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background:
    repeating-linear-gradient(
      0deg,
      transparent,
      transparent 2px,
      rgba(255,255,255,0.03) 2px,
      rgba(255,255,255,0.03) 4px
    );
  pointer-events: none;
}

.monster-face {
  width: 120px;
  height: 120px;
  background: linear-gradient(135deg, #a29bfe, #6c5ce7);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 40px;
  animation: wobble 2s ease-in-out infinite;
}

@keyframes wobble {
  0%, 100% { transform: rotate(-5deg); }
  50% { transform: rotate(5deg); }
}

.monster-eye {
  font-size: 36px;
  color: #fff;
}

.toxic-line {
  font-size: 18px;
  color: #fff;
  text-align: center;
  line-height: 1.6;
  margin-bottom: 50px;
  max-width: 300px;
}

.next-btn {
  padding: 16px 40px;
  background: rgba(255,255,255,0.1);
  border: 2px solid rgba(255,255,255,0.3);
  border-radius: 30px;
}

.next-btn:active {
  background: rgba(255,255,255,0.2);
}

.next-text {
  font-size: 16px;
  color: #fff;
}

/* ========== 第三步：疯狂摇晃 ========== */
.step-three {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: linear-gradient(180deg, #1a1a2e 0%, #16213e 100%);
  position: relative;
  overflow: hidden;
}

.shake-bg {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: radial-gradient(circle at center, transparent 30%, rgba(231, 76, 60, 0.1) 100%);
  transition: background 0.3s;
}

.shake-bg.shake-it {
  background: radial-gradient(circle at center, rgba(231, 76, 60, 0.3) 0%, rgba(142, 68, 173, 0.2) 100%);
}

.shake-title {
  font-size: 20px;
  color: #ff6b6b;
  text-align: center;
  margin-bottom: 40px;
  animation: shake-text 0.5s ease-in-out infinite;
}

@keyframes shake-text {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-3px); }
  75% { transform: translateX(3px); }
}

.chaos-button {
  width: 160px;
  height: 160px;
  border-radius: 50%;
  background: linear-gradient(145deg, #e74c3c, #ff6b6b);
  box-shadow: 0 10px 50px rgba(231, 76, 60, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  transition: transform 0.1s;
}

.chaos-emoji {
  font-size: 60px;
}

.shake-hint {
  margin-top: 30px;
  font-size: 14px;
  color: rgba(255,255,255,0.5);
  animation: pulse 1s ease-in-out infinite;
}

@keyframes pulse {
  0%, 100% { opacity: 0.5; }
  50% { opacity: 1; }
}

.shit-rain {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  pointer-events: none;
}

.shit {
  position: absolute;
  top: -30px;
  animation: fall-down 2s linear infinite;
}

@keyframes fall-down {
  0% { transform: translateY(0) rotate(0deg); }
  100% { transform: translateY(100vh) rotate(360deg); }
}

/* ========== 第四步：倒霉蛋卡片 ========== */
.step-four {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 30px;
  background: #1a1a2e;
  position: relative;
}

.card-bg {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background:
    radial-gradient(circle at 20% 80%, rgba(231, 76, 60, 0.2) 0%, transparent 50%),
    radial-gradient(circle at 80% 20%, rgba(142, 68, 173, 0.2) 0%, transparent 50%);
  pointer-events: none;
}

.loser-card {
  width: 320px;
  background: linear-gradient(145deg, #2d2d44, #1a1a2e);
  border: 2px solid rgba(255,255,255,0.1);
  border-radius: 20px;
  padding: 30px 25px;
  box-shadow: 0 20px 60px rgba(0,0,0,0.5);
}

.card-header {
  text-align: center;
  margin-bottom: 25px;
}

.card-label {
  font-size: 12px;
  color: rgba(255,255,255,0.5);
  text-transform: uppercase;
  letter-spacing: 3px;
}

.card-main-title {
  display: block;
  font-size: 22px;
  font-weight: 700;
  color: #fff;
  margin-top: 8px;
  background: linear-gradient(90deg, #ff6b6b, #ffd93d);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.card-body {
  text-align: center;
}

.card-subtitle {
  font-size: 13px;
  color: rgba(255,255,255,0.5);
}

.card-name {
  display: block;
  font-size: 16px;
  font-weight: 600;
  color: #a29bfe;
  margin-top: 10px;
  margin-bottom: 20px;
}

.card-divider {
  width: 60px;
  height: 2px;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.3), transparent);
  margin: 0 auto 20px;
}

.fortune-label {
  display: block;
  font-size: 11px;
  color: rgba(255,255,255,0.4);
  text-transform: uppercase;
  letter-spacing: 2px;
  margin-bottom: 10px;
}

.fortune-text {
  display: block;
  font-size: 14px;
  color: rgba(255,255,255,0.7);
  margin-bottom: 6px;
}

.fortune-quote {
  display: block;
  font-size: 13px;
  color: rgba(255,255,255,0.5);
  font-style: italic;
  margin-top: 20px;
  line-height: 1.5;
}

.card-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 25px;
  padding-top: 15px;
  border-top: 1px solid rgba(255,255,255,0.1);
}

.card-date {
  font-size: 12px;
  color: rgba(255,255,255,0.4);
}

.card-seal {
  font-size: 11px;
  color: rgba(255,255,255,0.3);
  padding: 4px 10px;
  border: 1px solid rgba(255,255,255,0.2);
  border-radius: 10px;
}

.exit-btn {
  margin-top: 40px;
  padding: 18px 35px;
  background: linear-gradient(135deg, #e74c3c, #8e44ad);
  border-radius: 30px;
  box-shadow: 0 10px 30px rgba(231, 76, 60, 0.3);
}

.exit-btn:active {
  transform: scale(0.95);
}

.exit-text {
  font-size: 15px;
  font-weight: 600;
  color: #fff;
}
</style>
