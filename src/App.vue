<template>
  <div id="app">
    <transition name="fade" v-if="showIntro">
      <div class="intro-container" @click="showIntro = false">
        <video
          class="video-background"
          :src="videoSrc"
          autoplay
          muted
          loop
          playsinline
        ></video>
        <div class="intro-content" aria-live="polite">
          <div class="intro-inner">
            <!-- 打字机展示的随机语句 -->
            <div class="typewriter-wrap">
              <p class="typewriter">
                {{ displayText }}
              </p>
            </div>
          </div>
        </div>
      </div>
    </transition>
    <div v-else>
      <navbar />

      <main class="main-content">
        <RouterView />
      </main>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from "vue";
import { RouterView } from "vue-router";
import navbar from "@/components/navbar.vue";

const showIntro = ref(true);
const videoSrc = ref(""); // 新增
const lines = [
  "镜中之华，视界殊异。于此寂静之地，你可窥见真实？", // 强调其异常视界，引发思考
  "气温正在下降……请不必担心，这只是我存在的证明。", // 从环境体感切入，点明冰霜特质
  "我的刀与誓言，皆为您此刻的安宁而驻守。", // 突出其近卫的忠诚与守护职责
  "你眼中的世界，是何色彩？在我的赤痕视界里，万物皆有另一副模样。", // 对比视角，突出角色独特性
  "踏雪无痕，但访客有声。欢迎你的到来。", // 结合能力（雪），表达对访客的感知
  "此间言语将凝作冰晶，请小心珍藏。", // 将“对话”比喻为可凝结珍藏的冰晶
  "长夜寒寂，守此一隅。你并非孤身一人。", // 营造寂静守护的氛围，给予陪伴感
  "我的右眼，映照着纷扰的‘声痕’。而此刻，它只倒映着一位访客。", // 聚焦“赤痕”特质，表达专注
  "沉默，是我的盔甲，亦是待客的礼数。请自便。", // 解释其沉默性格，并表达许可
  "冰封的并非时间，而是躁动的心绪。愿你能在此寻得片刻宁静。", // 升华“冰”的意境，赋予治愈感
  "警告：持续凝视我眼中的赤痕，可能导致认知冻结。不过，短暂的凝视是被允许的。", // 带有一丝克系的危险提示与邀请
  "遵循古老的刀禅，我守护着这片数据洪流中的‘静寂之土’。欢迎。", // 将网站空间比喻为其守护的领地
  "你的访问，如同一片雪花落入镜湖，我已察觉。", // 诗意地表达对访客到来的感知
  "低温能保存最纯粹的状态。那么，你此行的初衷，是否纯粹？", // 以低温为喻，进行略带哲思的提问
  "不必畏惧寒冷。真正的寒潮，往往源自灼热的纷争，而非寂静的冰霜。", // 为其冰冷特质辩护，蕴含哲理
] as const;

const displayText = ref("");

let typingTimer: number | null = null;

/* 打字机参数 */
const typingSpeed = 150; // 每字符间隔 ms

function pickRandomLine(): string {
  const idx = Math.floor(Math.random() * lines.length);
  return lines[idx];
}

/* 逐字打字，完成后启动 5s 隐藏计时（若用户偏好减少动效则直接显示且不自动隐藏） */
function startOnceType(line: string) {
  // 如果用户偏好减少动效，直接显示整句并不启动定时隐藏
  const reduce =
    window.matchMedia &&
    window.matchMedia("(prefers-reduced-motion: reduce)").matches;
  if (reduce) {
    displayText.value = line;
    return;
  }

  let i = 0;
  typingTimer = window.setInterval(() => {
    i++;
    displayText.value = line.slice(0, i);
    if (i >= line.length) {
      if (typingTimer) {
        clearInterval(typingTimer);
        typingTimer = null;
      }
    }
  }, typingSpeed);
}

onMounted(() => {
  // 检测是否为移动端
  const isMobile = window.innerWidth <= 768;
  const folder = isMobile ? "/mp2" : "/mp1";
  const index = Math.floor(Math.random() * 4) + 1; // 随机 1 或 2
  videoSrc.value = `${folder}/1 (${index}).mp4`;

  setTimeout(() => {
    showIntro.value = false;
  }, 6000); // 播放动画 4 秒后进入主页

  const line = pickRandomLine();
  // 延迟一点开始打字，视觉更舒适
  setTimeout(() => startOnceType(line), 200);
});

onBeforeUnmount(() => {
  if (typingTimer) clearInterval(typingTimer);
});
</script>

<style scoped lang="scss">
#app {
  position: relative;
  min-height: 100vh;
}

/* 淡入淡出动画 */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 1s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* 欢迎页样式 */
.intro-container {
  position: fixed;
  inset: 0;
  background: radial-gradient(ellipse at center, #111 0%, #000 100%);
  z-index: 9999;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  flex-direction: column;
}

.intro-content {
  width: 100%;
  box-sizing: border-box;
  padding: 18px 12px;
  display: flex;
  justify-content: center;
  align-items: center;

  z-index: 10;

  .intro-inner {
    display: flex;
    flex-direction: row;
    align-items: center;
    gap: 20px;
    transition: opacity 420ms ease, transform 420ms ease;
    opacity: 1;
    transform: translateY(0);

    .typewriter-wrap {
      display: flex;
      align-items: center;
      min-width: 0;

      .typewriter {
        margin: 0;
        font-weight: 700;
        font-size: clamp(36px, 7.6vw, 58px);
        line-height: 1.1;

        background: linear-gradient(90deg, #e9f7ff 0%, #78d0ff 100%);
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
        -webkit-text-fill-color: transparent;
        -webkit-text-stroke: 0.2px rgba(0, 0, 0, 0.06);
        text-shadow: 0 6px 18px rgba(60, 15, 29, 0.22);
      }
    }
  }
}

/* PC 横向（桌面）保持并列；移动端竖向 */
@media (max-width: 780px) {
  .intro-inner {
    flex-direction: column;
    gap: 8px;
    align-items: center;
    text-align: center;
  }

  .typewriter {
    font-size: clamp(10px, 5.8vw, 14px);

    max-width: 92vw;
  }
}

.video-background {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  opacity: 0.4;
  z-index: 1;
  pointer-events: none;
  /* 避免遮挡点击 */
}

/* 动画定义 */
@keyframes fadeIn {
  to {
    opacity: 1;
  }
}

@keyframes zoomIn {
  0% {
    transform: scale(0.95);
    opacity: 0.7;
  }

  100% {
    transform: scale(1);
    opacity: 1;
  }
}
</style>
