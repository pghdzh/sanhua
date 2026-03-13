<template>
  <header class="app-header">
    <h1 class="title">散华电子设定集</h1>
    <!-- 在线人数展示 -->
    <div class="online-count" v-if="onlineCount !== null">
      当前在线：<span class="count">{{ onlineCount }}人</span>
    </div>
    <button
      class="bgm-toggle"
      @click="toggleBgm"
      :aria-label="isBgmPlaying ? '暂停背景音乐' : '播放背景音乐'"
    >
      <i class="bgm-icon" :class="{ playing: isBgmPlaying }">♪</i>
      <span class="bgm-text">
        {{ isBgmPlaying ? "暂停" : "播放" }}
      </span>
    </button>
    <!-- 音频元素 -->
    <audio ref="bgmRef" src="/sanhua.mp3" loop preload="auto" />
    <!-- 移动端汉堡按钮 -->
    <button
      class="hamburger"
      @click="toggleMobileNav"
      aria-label="Toggle navigation"
    >
      <span :class="{ open: mobileNavOpen }"></span>
      <span :class="{ open: mobileNavOpen }"></span>
      <span :class="{ open: mobileNavOpen }"></span>
    </button>

    <!-- 普通导航 & 移动端下拉导航 -->
    <nav :class="['nav-links', { 'mobile-open': mobileNavOpen }]">
      <RouterLink
        v-for="item in navItems"
        :key="item.name"
        :to="item.path"
        class="nav-item"
        active-class="active-link"
        @click="mobileNavOpen = false"
      >
        {{ item.name }}
      </RouterLink>

      <a
        href="http://36.150.237.25/#/redirector"
        target="_blank"
        rel="noopener"
        class="nav-item"
        active-class="active-link"
        @click="mobileNavOpen = false"
      >
        霜落映界
      </a>
    </nav>
  </header>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from "vue";
import { io } from "socket.io-client";

const navItems = [
  { name: "赤痕誓心", path: "/" },
  { name: "雪踪时痕", path: "/timeLine" },
  { name: "冰语回响", path: "/message" },
  { name: "镜华视界", path: "/gallery" },
  { name: "霜刃典藏", path: "/resources" },
  { name: "镜华心音", path: "/talk" },
  { name: "凛声瞳影", path: "/voice" },
  { name: "刀鸣霜韵", path: "/music" },
  { name: "异观图录", path: "/wiki" },
  { name: "长夜梦守", path: "/thanks" },
];

const mobileNavOpen = ref(false);
function toggleMobileNav() {
  mobileNavOpen.value = !mobileNavOpen.value;
}

const siteId = "sanhua";

const onlineCount = ref<number | null>(null);

// 连接时带上 query.siteId
const socket: any = io(import.meta.env.VITE_API_BASE_URL, {
  query: { siteId },
});
const bgmRef = ref<HTMLAudioElement | null>(null);
const isBgmPlaying = ref(false);

function toggleBgm() {
  if (!bgmRef.value) return;

  if (isBgmPlaying.value) {
    bgmRef.value.pause();
    isBgmPlaying.value = false;
  } else {
    bgmRef.value.volume = 0.4; // 建议默认不要太大
    bgmRef.value.play().catch(() => {
      // 浏览器自动播放限制，需用户首次交互
    });
    isBgmPlaying.value = true;
  }
}
onMounted(() => {
  socket.on("onlineCount", (count: number) => {
    onlineCount.value = count;
  });
});

onBeforeUnmount(() => {
  socket.disconnect();
  bgmRef.value?.pause();
});
</script>
<style scoped lang="scss">
/* 散华 — 冷凝战装：冰晶霜银 / 铅灰切割 / 冷蓝高光 + 猩红印记 */
.app-header {
  /* 散华 — 优化配色（暗色主方案）*/
  --bg-ice-start: #061024; /* 用于 header 左上阴影 / 极深底 */
  --bg-ice-end: #0f2030; /* header 主体渐变深色 */
  --frost-core: #e9f7ff; /* 冰晶高光 / 标题亮色 */
  --accent-ice: #78d0ff; /* 冰蓝反光（hover / glint）*/
  --metal-gray: #18222a; /* 刀锋 / 文本深色基底 */
  --accent-crystal: rgba(120, 208, 255, 0.1); /* 晶体叠光 */
  --scarlet: #c84b4b; /* 猩红点缀 */
  --muted-text: #dbeefb; /* 主文字（深底上）*/
  --muted-sub: #9fb8c8; /* 次级文字 */
  --border: rgba(120, 208, 255, 0.06);
  --shadow-core: rgba(3, 8, 14, 0.6);

  position: fixed;
  inset: 0 0 auto 0;
  z-index: 1000;
  height: 64px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 40px;
  /* 冷凝金属 + 纤薄霜层纹理：线性渐变叠加晶体噪点 */

  /* 轻微玻璃/霜层折射 */
  backdrop-filter: blur(6px) saturate(1.05);
  box-shadow: 0 10px 28px var(--shadow-core),
    inset 0 1px 0 rgba(255, 255, 255, 0.02);
  border-bottom: 1px solid rgba(111, 208, 255, 0.04);
  animation: fadeInDown 0.36s ease-out both;
  overflow: visible;

  .title {
    position: relative;
    font-family: "Inter", "Noto Sans SC", sans-serif;
    font-size: 24px;
    font-weight: 800;
    color: var(--muted-text);
    /* 冰晶金属文字：冷银 -> 冰蓝 渐变 */
    background: linear-gradient(90deg, #e6f3fb 0%, var(--accent-ice) 55%);
    background-clip: text;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    letter-spacing: 0.3px;
    text-shadow: 0 6px 18px rgba(2, 6, 10, 0.42);
    transition: transform 0.22s ease, text-shadow 0.22s ease;
    animation: frost-glint 6.8s linear infinite;

    &:hover {
      transform: translateY(-2px) scale(1.02);
      text-shadow: 0 14px 30px rgba(6, 18, 28, 0.28);
    }

    /* 在标题左侧加一个细小的冰晶徽章（装饰） */
    &::before {
      content: "";
      display: inline-block;
      width: 10px;
      height: 10px;
      margin-right: 10px;
      vertical-align: middle;
      border-radius: 2px;
      background: linear-gradient(180deg, #ffffff, var(--frost-core));
      box-shadow: 0 2px 8px rgba(111, 208, 255, 0.12),
        0 0 24px rgba(111, 208, 255, 0.04) inset;
      transform: rotate(18deg);
    }
  }

  .online-count {
    position: relative;
    margin-left: 16px;
    padding: 6px 14px;
    font-family: "Noto Sans SC", "PingFang SC", sans-serif;
    font-size: 0.98rem;
    color: var(--muted-text);
    background: linear-gradient(
      180deg,
      rgba(12, 16, 22, 0.36),
      rgba(8, 12, 18, 0.24)
    );
    border: 1px solid rgba(111, 208, 255, 0.06);
    border-radius: 18px;
    backdrop-filter: blur(6px);
    box-shadow: 0 6px 14px rgba(2, 6, 10, 0.36);
    overflow: hidden;
    cursor: default;
    transition: transform 0.22s ease, box-shadow 0.22s ease;

    &:hover {
      transform: translateY(-2px);
      box-shadow: 0 14px 30px rgba(2, 6, 10, 0.48);
    }

    .count {
      display: inline-block;
      margin-left: 12px;
      font-weight: 800;
      color: var(--scarlet);
      background: linear-gradient(90deg, var(--accent-ice), var(--scarlet));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      text-shadow: 0 0 6px rgba(180, 70, 70, 0.04);
    }
  }
  .bgm-toggle {
    display: flex;
    align-items: center;
    gap: 8px;
    height: 36px;
    padding: 0 14px;
    border-radius: 18px;
    border: 1px solid rgba(120, 208, 255, 0.18);
    background: linear-gradient(
      180deg,
      rgba(12, 18, 26, 0.92),
      rgba(8, 12, 18, 0.92)
    );
    backdrop-filter: blur(6px);
    cursor: pointer;
    box-shadow: 0 6px 16px rgba(3, 8, 14, 0.6);
    transition: transform 0.2s ease, box-shadow 0.2s ease;

    &:hover {
      transform: translateY(-2px);
      box-shadow: 0 12px 28px rgba(3, 8, 14, 0.8);

      .bgm-text {
        max-width: 60px;
        opacity: 1;
      }
    }

    .bgm-icon {
      font-size: 18px;
      font-weight: 800;
      color: var(--accent-ice);
      text-shadow: 0 0 6px rgba(120, 208, 255, 0.4);
      transition: transform 0.2s ease, color 0.2s ease;

      &.playing {
        color: var(--scarlet);
        animation: bgm-spin 2.6s linear infinite;
      }
    }
    .bgm-text {
      font-size: 13px;
      color: var(--muted-text);
      white-space: nowrap;
      overflow: hidden;
      max-width: 0;
      opacity: 0;
      transition: max-width 0.24s ease, opacity 0.24s ease;
    }
  }

  .nav-links {
    display: flex;
    gap: 20px;
    align-items: center;

    .nav-item {
      position: relative;
      color: rgba(219, 233, 247, 0.9);
      font-weight: 600;
      text-decoration: none;
      transition: color 0.18s ease, transform 0.14s ease;
      padding: 6px 6px;
      border-radius: 6px;
      overflow: visible;
      font-family: "Inter", "Noto Sans SC", sans-serif;
      letter-spacing: 0.2px;

      &::after {
        /* 冰锋下划：锋利切割感的渐变条 */
        content: "";
        position: absolute;
        left: 50%;
        bottom: -8px;
        width: 0;
        height: 4px;
        border-radius: 2px;
        background: linear-gradient(
          90deg,
          rgba(111, 208, 255, 0),
          rgba(111, 208, 255, 0.95),
          rgba(180, 70, 70, 0.85)
        );
        transform: translateX(-50%) skewX(-8deg);
        opacity: 0.95;
        filter: blur(0.6px) contrast(1.01);
        transition: width 0.34s cubic-bezier(0.2, 0.9, 0.2, 1), left 0.34s,
          opacity 0.22s;
        background-size: 200% 100%;

        animation: brush-wave 8s ease-in-out infinite;
      }

      &::before {
        /* 小型刀锋徽记（hover 出现） */
        content: "";
        position: absolute;
        right: 18%;
        top: -6px;
        width: 8px;
        height: 8px;
        border-radius: 2px;
        background: linear-gradient(
          180deg,
          var(--frost-core),
          var(--accent-ice)
        );
        opacity: 0;
        transform: translateY(-6px) scale(0.9) rotate(12deg);
        transition: opacity 0.22s, transform 0.32s;
        box-shadow: 0 4px 10px rgba(60, 120, 160, 0.06);
      }

      &:hover {
        color: var(--accent-ice);
        transform: translateY(-1.6px);
      }

      &:hover::after {
        width: 64%;
        opacity: 1;
      }

      &:hover::before {
        opacity: 1;
        transform: translateY(0) scale(1) rotate(0deg);
      }
    }

    .active-link {
      color: #ffffff;
      font-weight: 700;

      &::before {
        /* 小猩红标识，代表散华的红瞳/印记 */
        content: "";
        position: absolute;
        right: -10px;
        top: 50%;
        transform: translateY(-50%) rotate(-6deg);
        width: 10px;
        height: 10px;
        border-radius: 2px;
        background: linear-gradient(
          180deg,
          var(--scarlet),
          rgba(180, 70, 70, 0.8)
        );
        box-shadow: 0 4px 14px rgba(180, 70, 70, 0.08);
      }

      &::after {
        width: 90%;
        opacity: 1;
        box-shadow: 0 8px 20px rgba(10, 20, 30, 0.18);
      }
    }
  }

  .hamburger {
    display: none;
    flex-direction: column;
    justify-content: space-around;
    width: 28px;
    height: 24px;
    background: none;
    border: none;
    cursor: pointer;
    padding: 0;

    span {
      display: block;
      width: 100%;
      height: 3px;
      background: linear-gradient(
        90deg,
        rgba(219, 233, 247, 0.92),
        rgba(111, 208, 255, 0.55)
      );
      border-radius: 2px;
      transition: transform 0.28s ease, opacity 0.28s ease, background 0.28s;
      box-shadow: 0 2px 6px rgba(0, 0, 0, 0.28);
    }

    span.open:nth-child(1) {
      transform: translateY(10px) rotate(45deg);
      background: linear-gradient(90deg, var(--accent-ice), var(--scarlet));
    }

    span.open:nth-child(2) {
      opacity: 0;
    }

    span.open:nth-child(3) {
      transform: translateY(-10px) rotate(-45deg);
      background: linear-gradient(90deg, var(--accent-ice), var(--scarlet));
    }
  }

  @media (max-width: 768px) {
    padding: 0 18px;

    .title {
      display: none;
    }
    .hamburger {
      display: flex;
    }

    .nav-links {
      position: absolute;
      top: 64px;
      left: 0;
      right: 0;
      flex-direction: column;
      background: linear-gradient(
        180deg,
        rgba(6, 10, 14, 0.94),
        rgba(8, 14, 20, 0.98)
      );
      backdrop-filter: blur(10px);
      gap: 0;
      overflow: hidden;
      max-height: 0;
      transition: max-height 0.34s ease;
      border-top: 1px solid rgba(111, 208, 255, 0.04);

      &.mobile-open {
        max-height: 520px;
      }

      .nav-item {
        padding: 14px 20px;
        border-bottom: 1px solid rgba(255, 255, 255, 0.03);
      }
    }
  }
}

/* 动效关键帧（霜光 / 冰晶闪烁 / 刀锋掠影 / 标记脉动） */
@keyframes frost-glint {
  0% {
    filter: drop-shadow(0 2px 6px rgba(6, 18, 28, 0.18));
    transform: translateY(0);
  }
  40% {
    transform: translateY(-1.5px);
    filter: drop-shadow(0 10px 18px rgba(6, 18, 28, 0.26));
  }
  100% {
    transform: translateY(0);
    filter: drop-shadow(0 2px 6px rgba(6, 18, 28, 0.18));
  }
}
@keyframes bgm-spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
@keyframes fadeInDown {
  0% {
    opacity: 0;
    transform: translateY(-8px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes brush-wave {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}
</style>
