<template>
  <main class="home">
    <canvas ref="canvasEl" class="rose-canvas" aria-hidden="true"></canvas>

    <!-- 背景轮播（两组用于桌面/移动不同裁切） -->
    <div class="carousel carousel1" aria-hidden="true">
      <img
        v-for="(src, idx) in randomFive"
        :key="idx"
        :src="src"
        class="carousel-image"
        :class="{ active: idx === currentIndex }"
      />
    </div>
    <div class="carousel carousel2" aria-hidden="true">
      <img
        v-for="(src, idx) in randomFive2"
        :key="idx"
        :src="src"
        class="carousel-image"
        :class="{ active: idx === currentIndex }"
      />
    </div>

    <section class="center" role="main">
      <h1 class="title">寒刃凝霜，立于今州 · 散华</h1>

      <div class="subtitle" aria-live="polite">
        <span class="typed">{{ typed }}</span
        ><span class="cursor" aria-hidden="true"></span>
      </div>
    </section>

    <footer
      class="shore-footer-simple"
      role="contentinfo"
      aria-label="页面页脚"
    >
      <div class="inner container">
        <div class="center">
          <div class="slogan">刃未冷，心常在</div>
          <div class="meta">
            © <span>{{ year }}</span> 散华电子设定集 · 制作：霜落天亦
          </div>
        </div>
      </div>
    </footer>
  </main>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from "vue";
import violet from "@/assets/violet.png"; // 若希望更贴合风格，可替换为“贝壳/羽毛/萤光点”贴图
const year = new Date().getFullYear();
const canvasEl = ref<HTMLCanvasElement | null>(null);
let ctx: CanvasRenderingContext2D;
let animationId = 0;
let lastTime = 0;
let elapsed = 0;

interface Rose {
  baseX: number;
  y: number;
  size: number;
  speed: number;
  swayAmp: number;
  swayFreq: number;
  phase: number;
  angle: number;
  angularSpeed: number;
}

const roses: Rose[] = [];
const ROSE_COUNT_DESKTOP = 18;
const ROSE_COUNT_MOBILE = 6;
const ROSE_IMG = new Image();
ROSE_IMG.src = violet;

function initRoses(count: number) {
  roses.length = 0;
  const canvas = canvasEl.value!;
  const w = canvas.width / (window.devicePixelRatio || 1);
  const h = canvas.height / (window.devicePixelRatio || 1);

  for (let i = 0; i < count; i++) {
    const baseX = Math.random() * w;
    roses.push({
      baseX,
      y: Math.random() * -h,
      size: 48 + Math.random() * 28, // 稍微精简尺寸
      speed: 12 + Math.random() * 36, // 速度更缓
      swayAmp: 12 + Math.random() * 26,
      swayFreq: 0.15 + Math.random() * 0.7,
      phase: Math.random() * Math.PI * 2,
      angle: Math.random() * Math.PI * 2,
      angularSpeed: (Math.random() - 0.5) * 1.2,
    });
  }
  elapsed = 0;
}

let resizeTimeout: number;
function resizeCanvas() {
  window.clearTimeout(resizeTimeout);
  resizeTimeout = window.setTimeout(() => {
    cancelAnimationFrame(animationId);
    const canvas = canvasEl.value!;
    const parent = canvas.parentElement!;
    const dpr = window.devicePixelRatio || 1;
    const w = parent.clientWidth;
    const h = Math.max(parent.clientHeight, 420); // 给个最小高度，避免太窄时粒子不明显

    canvas.style.width = w + "px";
    canvas.style.height = h + "px";
    canvas.width = w * dpr;
    canvas.height = h * dpr;

    ctx.setTransform(1, 0, 0, 1, 0, 0);
    ctx.scale(dpr, dpr);

    const isMobile = w < 768;
    initRoses(isMobile ? ROSE_COUNT_MOBILE : ROSE_COUNT_DESKTOP);
    lastTime = 0;
    animationId = requestAnimationFrame(tickCanvas);
  }, 160);
}

function tickCanvas(now: number) {
  if (!lastTime) lastTime = now;
  const dt = (now - lastTime) / 1000;
  lastTime = now;
  elapsed += dt;

  const canvas = canvasEl.value!;
  const w = canvas.clientWidth;
  const h = canvas.clientHeight;

  ctx.clearRect(0, 0, w, h);

  // 轻微整体雾层，增强深度（透明度低，避免影响可读性）
  ctx.fillStyle = "rgba(2,8,14,0.08)";
  ctx.fillRect(0, 0, w, h);

  roses.forEach((r) => {
    r.y += r.speed * dt;
    const sway = r.swayAmp * Math.sin(r.phase + elapsed * r.swayFreq);
    const x = r.baseX + sway;
    r.angle += r.angularSpeed * dt;

    if (r.y > h + r.size) {
      r.y = -r.size * 0.6;
      r.baseX = Math.random() * w;
      r.phase = Math.random() * Math.PI * 2;
    }

    if (x > w + r.size || x < -r.size) return;

    // 计算透明度：越远看上去越淡
    const alpha = Math.max(0, Math.min(1, 1 - (r.y / h) * 0.6));

    ctx.save();
    ctx.globalAlpha = alpha;
    ctx.translate(x, r.y);
    ctx.rotate(r.angle);

    if (ROSE_IMG && ROSE_IMG.complete && ROSE_IMG.naturalWidth > 0) {
      // 使用图片绘制，但加上一层冷色调叠加（globalCompositeOperation 简单处理）
      ctx.drawImage(ROSE_IMG, -r.size / 2, -r.size / 2, r.size, r.size);

      // 轻微冷光叠加，提升风格一致性
      ctx.globalCompositeOperation = "lighter";
      const grad = ctx.createRadialGradient(0, 0, 0, 0, 0, r.size);
      grad.addColorStop(0, `rgba(79,233,223,${0.08 * alpha})`);
      grad.addColorStop(1, "rgba(0,0,0,0)");
      ctx.fillStyle = grad;
      ctx.fillRect(-r.size / 2, -r.size / 2, r.size, r.size);
      ctx.globalCompositeOperation = "source-over";
    }

    ctx.restore();
  });

  animationId = requestAnimationFrame(tickCanvas);
}

// ========== 打字机文案 ==========
// 适合散华风格的副标题（偏长句，已为打字器准备）
const lines = [
  "鬼影侵袭，雪夜轮回，炼狱灼魂铸恶鬼",
  "春雷乍响，倒果为因，赤瞳朔雪护天光",
  "厨光照脸庞，霜雪映心光，龙包飘香在今朝",
  "寒刃未鸣，霜华已织；愿你于静默中，见一缕光。",
  "今州边庭，刀影与雪共舞；这是散华留下的片段。",
  "立于尘世如刀刃，清冷却不失温度——那是守望。",
  "若你愿停步，请随我一同游历冰与锋的交错。",
  "在这里，每一处裂痕都是过往的印记，亦是一道风景。",
  "我以刃作笔，用霜写下无法言表的念想。",
];

const typed = ref("");
let lineIndex = 0;
let charIndex = 0;
let deleting = false;
let timer: number | null = null;

const TYPING = 120;
const DELETING = 40;
const PAUSE = 1200;

function tick() {
  const cur = lines[lineIndex];
  if (!deleting) {
    typed.value = cur.slice(0, charIndex + 1);
    charIndex++;
    if (charIndex >= cur.length) {
      timer = window.setTimeout(() => {
        deleting = true;
        tick();
      }, PAUSE);
      return;
    }
    timer = window.setTimeout(tick, TYPING);
  } else {
    typed.value = cur.slice(0, charIndex - 1);
    charIndex--;
    if (charIndex <= 0) {
      deleting = false;
      lineIndex = (lineIndex + 1) % lines.length;
      timer = window.setTimeout(tick, 360);
      return;
    }
    timer = window.setTimeout(tick, DELETING);
  }
}

// ========== 背景图片导入与轮播 ==========
const modules = import.meta.glob("@/assets/images1/*.{jpg,png,jpeg,webp}", {
  eager: true,
});
const allSrcs: string[] = Object.values(modules).map((mod: any) => mod.default);

const modules2 = import.meta.glob("@/assets/images2/*.{jpg,png,jpeg,webp}", {
  eager: true,
});
const allSrcs2: string[] = Object.values(modules2).map(
  (mod: any) => mod.default
);

function shuffle<T>(arr: T[]): T[] {
  const a = arr.slice();
  for (let i = a.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [a[i], a[j]] = [a[j], a[i]];
  }
  return a;
}
const randomFive = ref<string[]>(shuffle(allSrcs).slice(0, 5));
const randomFive2 = ref<string[]>(shuffle(allSrcs2).slice(0, 5));

const currentIndex = ref(0);
let Imgtimer: number | undefined;

onMounted(() => {
  timer = window.setTimeout(tick, 420);

  Imgtimer = window.setInterval(() => {
    currentIndex.value =
      (currentIndex.value + 1) % Math.max(1, randomFive.value.length);
  }, 5200);

  const canvas = canvasEl.value!;
  ctx = canvas.getContext("2d")!;

  // 当图片加载或资源就绪后调整 canvas 大小并启动渲染
  ROSE_IMG.onload = () => {
    resizeCanvas();
  };
  // 如果图片已经加载完（缓存情况），也要触发 init
  if (ROSE_IMG.complete && ROSE_IMG.naturalWidth > 0) {
    resizeCanvas();
  }

  window.addEventListener("resize", resizeCanvas);
});

onBeforeUnmount(() => {
  if (Imgtimer) clearInterval(Imgtimer);
  if (timer) window.clearTimeout(timer);

  cancelAnimationFrame(animationId);
  window.removeEventListener("resize", resizeCanvas);
});
</script>

<style lang="scss" scoped>
@use "sass:color";

/* 散华风格 - 冷凝 / 刃霜 / 猩红点缀 */
$ice-start: #061024; // 极夜深底（header / 边缘用）
$ice-end: #eaf6ff; // 冰光近白（高光用，局部）
$frost-core: #dff7ff; // 冰晶高光
$accent-ice: #78d0ff; // 冷蓝反光（hover / glint）
$blade-gray: #162029; // 刀锋 / 金属暗色
$glass-layer: rgba(8, 12, 18, 0.6); // 半透明玻璃层
$crystal-glow: rgba(120, 208, 255, 0.08);
$scarlet: #c84b4b; // 猩红点缀（极少量）
$text-main: #dbeefb; // 深底上的主文字
$text-strong: #bcd9ec; // 次级文字
$grain: rgba(255, 255, 255, 0.03); // 细微冰晶/颗粒
$shadow-core: rgba(3, 8, 14, 0.6);

/* 基本布局 */
.home {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  position: relative;
  overflow: hidden;
  color: $text-main;
  font-family: Inter, "PingFang SC", "Noto Sans CJK SC", "Microsoft YaHei",
    sans-serif;

  /* 背景：夜色深底 + 冰光渐变 + 冰晶泛光 */
  background: radial-gradient(
      circle at 12% 14%,
      rgba($accent-ice, 0.03),
      transparent 8%
    ),
    radial-gradient(circle at 84% 76%, rgba($frost-core, 0.02), transparent 8%),
    linear-gradient(180deg, $ice-start 0%, $ice-end 100%);

  /* 冰晶斜纹（替代纸纤维） */
  &::before {
    content: "";
    position: absolute;
    inset: -8% -10%;
    z-index: 1;
    background-image: repeating-linear-gradient(
      135deg,
      $grain 0px,
      rgba(255, 255, 255, 0) 2px,
      rgba(255, 255, 255, 0) 28px
    );
    opacity: 0.55;
    mix-blend-mode: overlay;
    pointer-events: none;
    filter: contrast(0.98) saturate(0.98);
    transform: translateY(-2%);
  }

  /* 背景占位（可放冰晶 / 立绘阴影 SVG） */
  .rose-canvas {
    position: absolute;
    inset: 0;
    z-index: 2;
    pointer-events: none;
    /* 若使用 svg.crane-shadow 可放在此 dom，建议 mix-blend-mode: screen 或 overlay */
  }

  /* 轮播区：保留淡入与冰层滤镜 */
  .carousel {
    position: absolute;
    inset: 0;
    z-index: 0;
    pointer-events: none;

    &::before {
      content: "";
      position: absolute;
      inset: 0;
      /* 细微竖向/斜向纹理模拟霜层 */
      background-image: repeating-linear-gradient(
        to bottom,
        rgba(255, 255, 255, 0.01) 0px,
        rgba(255, 255, 255, 0.01) 1px,
        transparent 1px,
        transparent 18px
      );
      opacity: 0.06;
      mix-blend-mode: overlay;
      z-index: 2;
      transform: translateY(-2%);
      animation: ice-wave 18s linear infinite;
      pointer-events: none;
    }

    .carousel-image {
      position: absolute;
      width: 100%;
      height: 100%;
      object-fit: cover;
      opacity: 0;
      transition: opacity 700ms ease, transform 10s linear;
      filter: blur(0.6px) saturate(0.9) contrast(0.96) brightness(0.96);
      transform: scale(1.02);
      mix-blend-mode: multiply;

      &.active {
        opacity: 1;
        transform: scale(1);
      }
    }
  }

  .carousel2 {
    display: none;
  }

  /* 中央主内容 */
  .center {
    position: relative;
    z-index: 4;
    flex: 1 0 auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    padding: 40px 24px;
    gap: 10px;

    /* 主标题：冰晶金属风 */
    .title {
      font-size: 4.2rem;
      font-weight: 900;
      margin: 0;
      line-height: 1;
      /* 冰晶渐变文字 */
      background: linear-gradient(90deg, $frost-core 0%, $accent-ice 70%);
      -webkit-background-clip: text;
      background-clip: text;
      -webkit-text-fill-color: transparent;
      color: $text-main;
      letter-spacing: 0.2px;
      position: relative;
      display: inline-block;
      padding: 8px 10px;
      transform-origin: center;
      transition: transform 0.28s ease, text-shadow 0.28s ease;
      -webkit-text-stroke: 0.4px rgba(2, 6, 10, 0.18);

      /* 冰光反射微粒 */
      &::after {
        content: "";
        position: absolute;
        inset: -2px -6px;
        background: linear-gradient(
          90deg,
          rgba(255, 255, 255, 0.02),
          rgba(120, 208, 255, 0.02)
        );
        border-radius: 6px;
        z-index: -1;
        filter: blur(6px);
        opacity: 0.6;
        pointer-events: none;
      }

      &:hover {
        transform: translateY(-3px) scale(1.01);
      }
    }

    /* 副标题：保留书法气质但色调偏冷 */
    .subtitle {
      font-size: 2.2rem;
      min-height: 1.6em;
      color: color.adjust($text-main, $lightness: -6%); /* 更深一点提高可读性 */
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 10px;
      font-family: "STKaiti", "Noto Serif SC", cursive;
      font-weight: 500;
      letter-spacing: 0.04em;
      position: relative;
      padding: 8px 14px;

      /* 半透明“霜带”底，增加对比同时不过分破坏背景 */
      &::before {
        content: "";
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        width: calc(100% + 36px);
        height: calc(100% + 6px);
        border-radius: 6px;
        background: linear-gradient(
          90deg,
          rgba(234, 246, 255, 0.1),
          rgba(12, 18, 24, 0.72)
        );
        box-shadow: 0 6px 18px rgba(6, 8, 12, 0.04) inset;
        z-index: -1;
        pointer-events: none;
        opacity: 0.98;
        mix-blend-mode: normal;
        filter: blur(0.4px);
      }

      .typed {
        display: inline-block;
        font-weight: 700;
        color: color.adjust($text-main, $lightness: -10%);
        letter-spacing: 0.06em;
      }

      .cursor {
        width: 10px;
        height: 1.05em;
        margin-left: 6px;
        background: linear-gradient(180deg, $scarlet, $accent-ice);
        border-radius: 2px;
        animation: blink 1s steps(1) infinite;
        transform: translateY(2px);
        filter: drop-shadow(0 6px 14px rgba($scarlet, 0.06));
      }
    }
  }

  /* 页脚：冰霜收口 + 猩红细线 */
  .shore-footer-simple {
    background: linear-gradient(
      180deg,
      rgba(8, 12, 18, 0.22),
      rgba(12, 18, 26, 0.64)
    );
    border-top: 1px solid rgba($scarlet, 0.04);
    color: $text-main;
    font-size: 13px;
    position: relative;
    overflow: visible;
    z-index: 3;

    backdrop-filter: blur(3px);
    .inner.container {
      width: min(1100px, 94%);
      margin: 0 auto;
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 12px;
    }

    .center {
      text-align: center;
      flex: 1 1 auto;

      .slogan {
        background: linear-gradient(90deg, $frost-core 0%, $accent-ice 60%);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        display: inline-block;
        line-height: 1;
        font-size: 14px;
        letter-spacing: 0.3px;
        text-shadow: 0 6px 18px rgba(6, 10, 14, 0.06);
      }

      .meta {
        color: rgba($text-main, 0.66);
        margin-top: 6px;
        font-size: 12px;
      }
    }
  }
}

/* 单独浮动印记 / 纸片（可插入 .floating-note）*/
.floating-note {
  position: absolute;
  font-size: 14px;
  color: $scarlet;
  opacity: 0.95;
  transform-origin: center;
  animation: mark-float 5.6s ease-in-out infinite;
  filter: drop-shadow(0 6px 18px rgba($scarlet, 0.06));
  z-index: 6;
}

/* === 动画关键帧（冰层波动 / 冰光闪 / 碎片掠过 / 标记漂浮） === */
@keyframes ice-wave {
  0% {
    transform: translateY(-6%);
    opacity: 0.9;
  }
  50% {
    transform: translateY(6%);
    opacity: 1;
  }
  100% {
    transform: translateY(-6%);
    opacity: 0.9;
  }
}

@keyframes frost-glint {
  0% {
    transform: translateY(0);
    filter: drop-shadow(0 2px 6px rgba(6, 18, 28, 0.12));
  }
  45% {
    transform: translateY(-1.6px);
    filter: drop-shadow(0 10px 18px rgba(6, 18, 28, 0.22));
  }
  100% {
    transform: translateY(0);
    filter: drop-shadow(0 2px 6px rgba(6, 18, 28, 0.12));
  }
}

/* 碎片掠过短动画（用于切换等突显） */
@keyframes shard-swipe {
  0% {
    width: 0;
    opacity: 0;
    transform: skewX(-8deg) translateX(-12px);
  }
  40% {
    width: 120%;
    opacity: 1;
    transform: skewX(-8deg) translateX(6px);
  }
  100% {
    width: 0;
    opacity: 0;
    transform: skewX(-8deg) translateX(28px);
  }
}

@keyframes mark-float {
  0% {
    transform: translateY(0) rotate(-3deg) scale(0.98);
    opacity: 0.86;
  }
  50% {
    transform: translateY(-8px) rotate(2deg) scale(1.02);
    opacity: 1;
  }
  100% {
    transform: translateY(0) rotate(-3deg) scale(0.98);
    opacity: 0.86;
  }
}

@keyframes blink {
  0% {
    opacity: 1;
  }
  50% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

/* 响应式（移动优先） */
@media (max-width: 720px) {
  .home {
    .carousel {
      display: none;
    }
    .carousel2 {
      display: block;
    }

    .center {
      padding: 18px 14px;

      .title {
        font-size: 2.2rem;
      }
      .subtitle {
        font-size: 1.4rem;
      }
    }
  }
}
</style>
