<template>
  <view class="page" :class="{ 'page-bright': state === 'result' }">
    <!-- 初始状态：大按钮 -->
    <view v-if="state === 'initial'" class="initial-view">
      <text class="question">迟到了么？</text>
      <view
        class="main-button"
        :class="{ 'button-pressed': isPressed }"
        @touchstart="onTouchStart"
        @touchend="onTouchEnd"
        @click="handleClick"
      >
        <text class="button-text">迟到了!!</text>
      </view>
      <text class="hint">点我发泄一下</text>
    </view>

    <!-- 爆炸动画状态 -->
    <view v-if="state === 'exploding'" class="explosion-view">
      <view class="explosion-center" :class="{ 'explode': state === 'exploding' }">
        <text class="explode-text">啊!!!</text>
      </view>
      <!-- 飞出的情绪图片 -->
      <view
        v-for="(emoji, index) in emojis"
        :key="index"
        class="flying-emoji"
        :style="getEmojiStyle(index)"
      >
        {{ emoji }}
      </view>
    </view>

    <!-- 结果状态：统计数据 -->
    <view v-if="state === 'result'" class="result-view">
      <view class="result-card">
        <text class="result-title">今日已有</text>
        <text class="result-count">{{ todayCount }}</text>
        <text class="result-unit">人迟到</text>
      </view>
      <view class="result-divider"></view>
      <view class="result-card">
        <text class="result-title">你是第</text>
        <text class="result-rank">{{ myRank }}</text>
        <text class="result-unit">位</text>
      </view>
      <view class="comfort-text">
        <text>你不是一个人在战斗 💪</text>
      </view>
      <view class="again-button" @click="reset">
        <text class="again-text">再来一次</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      state: 'initial', // initial -> exploding -> result
      isPressed: false,
      emojis: ['😤', '😫', '🙄', '😮‍💨', '💢', '😡', '🤬', '💔', '🌩️', '💥', '🔥', '💀', '🥲', '😑', '🫠'],
      emojiPositions: [],
      todayCount: 0,
      myRank: 0
    }
  },
  onLoad() {
    this.generateEmojiPositions()
    this.loadMockData()
  },
  methods: {
    onTouchStart() {
      this.isPressed = true
    },
    onTouchEnd() {
      this.isPressed = false
    },
    handleClick() {
      if (this.state !== 'initial') return
      this.state = 'exploding'

      // 更新统计数据
      this.myRank = this.todayCount + 1
      this.todayCount++

      // 动画结束后显示结果
      setTimeout(() => {
        this.state = 'result'
      }, 1500)
    },
    reset() {
      this.state = 'initial'
      this.generateEmojiPositions()
    },
    generateEmojiPositions() {
      this.emojiPositions = this.emojis.map(() => ({
        x: (Math.random() - 0.5) * 400,
        y: (Math.random() - 0.5) * 400,
        rotate: Math.random() * 720 - 360,
        delay: Math.random() * 0.3,
        scale: 0.8 + Math.random() * 0.8
      }))
    },
    getEmojiStyle(index) {
      const pos = this.emojiPositions[index]
      if (!pos) return {}
      return {
        '--tx': `${pos.x}px`,
        '--ty': `${pos.y}px`,
        '--rotate': `${pos.rotate}deg`,
        '--delay': `${pos.delay}s`,
        '--scale': pos.scale
      }
    },
    loadMockData() {
      // 模拟数据：今日已有 1000-5000 人迟到
      this.todayCount = Math.floor(Math.random() * 4000) + 1000
    }
  }
}
</script>

<style scoped>
.page {
  min-height: 100vh;
  background: linear-gradient(180deg, #1a1a2e 0%, #16213e 100%);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  transition: background 0.5s ease;
}

.page-bright {
  background: linear-gradient(180deg, #2d3436 0%, #636e72 100%);
}

/* 初始状态样式 */
.initial-view {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.question {
  font-size: 48px;
  font-weight: bold;
  color: #e94560;
  margin-bottom: 60px;
  text-shadow: 0 0 20px rgba(233, 69, 96, 0.5);
}

.hint {
  font-size: 14px;
  color: #666;
  margin-top: 30px;
}

.main-button {
  width: 180px;
  height: 180px;
  border-radius: 50%;
  background: linear-gradient(145deg, #e94560, #c73e54);
  box-shadow:
    0 10px 30px rgba(233, 69, 96, 0.4),
    0 0 60px rgba(233, 69, 96, 0.2),
    inset 0 -5px 20px rgba(0, 0, 0, 0.2);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: transform 0.15s ease, box-shadow 0.15s ease;
  animation: pulse 2s ease-in-out infinite;
}

.main-button:active,
.button-pressed {
  transform: scale(1.1);
  box-shadow:
    0 15px 40px rgba(233, 69, 96, 0.6),
    0 0 80px rgba(233, 69, 96, 0.4);
}

@keyframes pulse {
  0%, 100% {
    box-shadow:
      0 10px 30px rgba(233, 69, 96, 0.4),
      0 0 60px rgba(233, 69, 96, 0.2);
  }
  50% {
    box-shadow:
      0 10px 30px rgba(233, 69, 96, 0.4),
      0 0 80px rgba(233, 69, 96, 0.4);
  }
}

.button-text {
  font-size: 24px;
  font-weight: bold;
  color: #fff;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
}

/* 爆炸动画样式 */
.explosion-view {
  position: relative;
  width: 100%;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}

.explosion-center {
  width: 200px;
  height: 200px;
  border-radius: 50%;
  background: linear-gradient(145deg, #e94560, #ff6b6b);
  display: flex;
  align-items: center;
  justify-content: center;
  animation: explode 0.5s ease-out forwards;
}

.explode-text {
  font-size: 36px;
  font-weight: bold;
  color: #fff;
}

@keyframes explode {
  0% {
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(2);
    opacity: 0.8;
  }
  100% {
    transform: scale(3);
    opacity: 0;
  }
}

.flying-emoji {
  position: absolute;
  top: 50%;
  left: 50%;
  font-size: 40px;
  animation: fly 1s ease-out forwards;
  animation-delay: var(--delay);
  transform-origin: center;
}

@keyframes fly {
  0% {
    transform: translate(-50%, -50%) scale(0.5);
    opacity: 1;
  }
  100% {
    transform: translate(
      calc(-50% + var(--tx)),
      calc(-50% + var(--ty))
    ) scale(var(--scale)) rotate(var(--rotate));
    opacity: 0;
  }
}

/* 结果状态样式 */
.result-view {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 40px;
}

.result-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 30px 60px;
}

.result-title {
  font-size: 18px;
  color: #aaa;
}

.result-count {
  font-size: 72px;
  font-weight: bold;
  color: #e94560;
  margin: 10px 0;
  text-shadow: 0 0 30px rgba(233, 69, 96, 0.5);
}

.result-rank {
  font-size: 56px;
  font-weight: bold;
  color: #ffd93d;
  margin: 10px 0;
  text-shadow: 0 0 30px rgba(255, 217, 61, 0.5);
}

.result-unit {
  font-size: 18px;
  color: #aaa;
}

.result-divider {
  width: 200px;
  height: 1px;
  background: linear-gradient(90deg, transparent, #666, transparent);
}

.comfort-text {
  margin-top: 30px;
}

.comfort-text text {
  font-size: 16px;
  color: #888;
}

.again-button {
  margin-top: 40px;
  padding: 15px 40px;
  background: linear-gradient(145deg, #e94560, #c73e54);
  border-radius: 30px;
  box-shadow: 0 5px 20px rgba(233, 69, 96, 0.4);
}

.again-button:active {
  transform: scale(0.95);
}

.again-text {
  font-size: 16px;
  color: #fff;
  font-weight: bold;
}
</style>