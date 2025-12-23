<template>
  <div class="megumi-message-board" aria-live="polite">
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
    <!-- 半透明顶部标题 -->
    <header class="board-header" role="banner">
      <div class="title-wrap">
        <h1>留言板</h1>
        <span class="title-count">（共{{ count }}条）</span>

        <p class="subtitle">言凝霜晶，痕刻镜华</p>
      </div>
    </header>

    <!-- 留言展示区 -->
    <section class="message-list">
      <transition-group name="msg" tag="div" class="message-list-inner">
        <div v-if="loading" class="skeleton-wrap" key="skeleton">
          <div class="skeleton" v-for="i in 1" :key="i">
            <div class="sk-avatar"></div>
            <div class="sk-lines">
              <div class="sk-line short"></div>
              <div class="sk-line"></div>
            </div>
          </div>
        </div>
        <div
          v-for="(msg, idx) in messages"
          :key="msg.id || msg._tempId || idx"
          class="message-card"
          :data-index="idx"
          tabindex="0"
          role="article"
          :aria-label="`留言来自 ${msg.name || '匿名'}，内容：${msg.content}`"
        >
          <div class="message-meta">
            <div class="left-meta">
              <div class="name-avatar" :title="msg.name || '匿名'">
                {{ getInitial(msg.name) }}
              </div>
              <div class="meta-texts">
                <div class="message-name">{{ msg.name || "匿名" }}</div>
                <div class="message-time">{{ formatTime(msg.created_at) }}</div>
              </div>
            </div>
          </div>

          <p class="message-content">{{ msg.content }}</p>
        </div>
      </transition-group>
    </section>

    <!-- 底部发送区 -->
    <section class="message-form" aria-label="写下你的留言">
      <label class="sr-only" for="mb-name">你的昵称</label>
      <input
        id="mb-name"
        v-model="name"
        type="text"
        placeholder="你的昵称"
        @keydown.enter.prevent
      />

      <label class="sr-only" for="mb-content">留言内容</label>
      <textarea
        id="mb-content"
        v-model="content"
        placeholder="写下你的留言..."
        @keydown.ctrl.enter.prevent="submitMessage"
        @input="autoGrow"
        ref="textareaRef"
      />

      <div class="form-row">
        <div class="hint">按 <kbd>Ctrl</kbd> + <kbd>Enter</kbd> 快捷发送</div>
        <button @click="submitMessage" :disabled="isSending || !content.trim()">
          <span v-if="!isSending">发送</span>
          <span v-else>发送中…</span>
        </button>
      </div>
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, nextTick, onBeforeUnmount } from "vue";
import { getMessageList, createMessage } from "@/api/modules/message";

const messages = ref<any[]>([]);
const count = ref(0);
const name = ref(localStorage.getItem("message_name") || "");
const content = ref("");
const loading = ref(true);
const isSending = ref(false);

const textareaRef = ref<HTMLTextAreaElement | null>(null);

const fetchMessages = async () => {
  loading.value = true;
  try {
    const res = await getMessageList({ page: 1, pageSize: 999 });
    messages.value = res.data || [];
    count.value = res.pagination.total;
    await nextTick();
  } catch (err) {
    console.error(err);
  } finally {
    loading.value = false;
  }
};

const submitMessage = async () => {
  if (!content.value.trim() || isSending.value) return;
  isSending.value = true;
  const payload = { name: name.value || "匿名", content: content.value };
  try {
    localStorage.setItem("message_name", name.value);
    content.value = "";
    await nextTick();
    // 发送请求
    await createMessage(payload);
    // 重新同步列表（更可靠）
    await fetchMessages();
  } catch (err) {
    console.error(err);
  } finally {
    isSending.value = false;
  }
};

const formatTime = (time: string) => {
  if (!time) return "";
  const d = new Date(time);
  // 例如：2025-08-11 15:30
  const y = d.getFullYear();
  const m = String(d.getMonth() + 1).padStart(2, "0");
  const day = String(d.getDate()).padStart(2, "0");
  const hh = String(d.getHours()).padStart(2, "0");
  const mm = String(d.getMinutes()).padStart(2, "0");
  return `${y}-${m}-${day} ${hh}:${mm}`;
};

const getInitial = (n?: string) => {
  if (!n) return "匿";
  return n.trim().slice(0, 1).toUpperCase();
};

const autoGrow = (e?: Event) => {
  const ta = textareaRef.value;
  if (!ta) return;
  ta.style.height = "auto";
  const h = Math.min(ta.scrollHeight, 220);
  ta.style.height = h + "px";
};

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
  fetchMessages();
  Imgtimer = window.setInterval(() => {
    currentIndex.value =
      (currentIndex.value + 1) % Math.max(1, randomFive.value.length);
  }, 5200);
  nextTick(() => autoGrow());
});

onBeforeUnmount(() => {
  if (Imgtimer) clearInterval(Imgtimer);
});
</script>

<style scoped lang="scss">
@use "sass:color";

/* ========== 变量（散华风） ========== */
/* 冷色为主，猩红仅作点缀 */
$bg-deep: #051025; // 极夜深底
$deep-2: #0b2432; // 中层渐变
$ice-core: #eaf7ff; // 冰晶高光（文字/渐变起点）
$accent-ice: #6fd0ff; // 冷蓝反光（hover / glint）
$blade-gray: #162029; // 刀锋 / 深色基底
$glass-layer: rgba(8, 12, 18, 0.64); // 半透明玻璃层
$crystal-glow: rgba(120, 208, 255, 0.06);
$scarlet: #c84b4b; // 猩红点缀（极少量）
$text-main: #dbeefb; // 主文字（浅色，用于深底）
$text-muted: rgba(219, 234, 247, 0.72);

$card-bg: rgba(6, 10, 16, 0.56); // 卡片背景（暗玻璃）
$card-border: rgba(111, 208, 255, 0.04); // 卡片边框（微冰蓝）
$soft-shadow: rgba(3, 8, 14, 0.6);
$inner-glow: rgba(120, 208, 255, 0.03);
$grain: rgba(255, 255, 255, 0.02);

/* ========== 全局容器 ========== */
.megumi-message-board {
  position: relative;
  min-height: 100vh;
  padding-top: 110px;
  display: flex;
  flex-direction: column;
  background: radial-gradient(
      circle at 12% 18%,
      rgba($accent-ice, 0.03),
      transparent 6%
    ),
    radial-gradient(circle at 78% 70%, rgba($ice-core, 0.02), transparent 6%),
    linear-gradient(180deg, $bg-deep 0%, $deep-2 100%);
  font-family: "Noto Sans SC", "Noto Sans", system-ui, -apple-system, "Segoe UI",
    Roboto, Arial;
  color: $text-main;
  overflow: hidden;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;

  /* 冰晶斜纹 / 微粒层（代替纸纤维） */
  &::after {
    content: "";
    position: absolute;
    inset: 0;
    z-index: 0;
    pointer-events: none;
    background-image: repeating-linear-gradient(
        to right,
        rgba(4, 8, 12, 0.02) 0px,
        rgba(255, 255, 255, 0) 1px,
        rgba(255, 255, 255, 0) 28px
      ),
      radial-gradient(
        circle at 14% 18%,
        rgba($accent-ice, 0.03),
        transparent 8%
      ),
      radial-gradient(circle at 72% 40%, rgba($ice-core, 0.02), transparent 8%);
    mix-blend-mode: multiply;
    filter: blur(2.6px) saturate(0.96);
    opacity: 0.82;
  }

  /* 预留鹤影 SVG 容器（插入时使用 fill="currentColor"） */
  .rose-canvas {
    position: absolute;
    inset: 0;
    z-index: 1;
    pointer-events: none;

    .crane-shadow {
      position: absolute;
      right: 6%;
      top: 6%;
      width: 420px;
      height: 120px;
      pointer-events: none;
      color: rgba(220, 240, 255, 0.04);
      opacity: 0.08;
      filter: blur(6px) contrast(1.02);
      transform: translateY(0) rotate(-3deg);
      animation: crane-drift 12s ease-in-out infinite;
      mix-blend-mode: multiply;
    }

    @media (max-width: 720px) {
      .crane-shadow {
        display: none;
      }
    }
  }

  /* 背景轮播（覆盖层） */
  .carousel {
    position: absolute;
    inset: 0;
    z-index: 0;
    pointer-events: none;

    &::before {
      content: "";
      position: absolute;
      inset: 0;
      background: linear-gradient(
        180deg,
        rgba(6, 6, 8, 0.12),
        rgba(6, 6, 8, 0.18)
      );
      z-index: 1;
      mix-blend-mode: soft-light;
      pointer-events: none;
    }

    .carousel-image {
      position: absolute;
      width: 100%;
      height: 100%;
      object-fit: cover;
      opacity: 0;
      transition: opacity 900ms ease, transform 12s linear;
      filter: blur(0.6px) saturate(0.85) contrast(0.95) brightness(0.94);
      transform: scale(1.03);

      &.active {
        opacity: 1;
        transform: scale(1);
      }
    }
  }

  .carousel2 {
    display: none;
  }

  /* ========== 顶部板头（冰霜玻璃感） ========== */
  .board-header {
    position: absolute;
    top: 72px;
    left: 50%;
    transform: translateX(-50%);
    width: calc(100% - 32px);
    max-width: 960px;
    padding: 14px 18px;
    border-radius: 14px;
    box-shadow: 0 18px 48px rgba(0, 0, 0, 0.7), inset 0 1px 0 $inner-glow;
    backdrop-filter: blur(10px) saturate(0.9);
    z-index: 6;
    border: 1px solid rgba($accent-ice, 0.02);
    background: linear-gradient(
      180deg,
      rgba(10, 14, 18, 0.36),
      rgba(6, 10, 12, 0.48)
    );

    &::before {
      content: "";
      position: absolute;
      inset: 6% 0 6% 0;
      z-index: -1;
      pointer-events: none;
      background-image: repeating-linear-gradient(
        to bottom,
        rgba(255, 255, 255, 0.01) 0px,
        rgba(255, 255, 255, 0.01) 1px,
        transparent 1px,
        transparent 18px
      );
      opacity: 0.04;
      mix-blend-mode: overlay;
      transform: translateY(-8%);
      animation: staff-scroll 20s linear infinite;
    }

    .title-wrap {
      display: flex;
      align-items: center;
      gap: 12px;
    }

    .title-wrap h1 {
      margin: 0;
      font-size: 18px;
      color: $ice-core;
      letter-spacing: 0.4px;
      font-weight: 900;
      text-shadow: 0 6px 16px rgba(2, 6, 10, 0.5);
    }

    .title-count {
      color: $text-muted;
      font-size: 12px;
      font-weight: 700;
      margin-left: 6px;
    }
    .subtitle {
      margin-left: auto;
      color: $text-muted;
      font-size: 13px;
      font-weight: 600;
      text-shadow: 0 1px 6px rgba($accent-ice, 0.02);
    }
  }

  /* ========== 留言列表（优化，暗版） ========== */
  .message-list {
    z-index: 2;
    position: relative;
    flex: 1;
    overflow-y: auto;
    padding: 28px 20px 340px;
    margin-top: 18px;

    .message-list-inner {
      max-width: 960px;
      max-height: 80vh;
      margin: 0 auto;
      display: flex;
      flex-direction: column;
      gap: 16px;
      position: relative;
      z-index: 2;
      overflow-y: auto;
    }

    /* skeleton 占位优化（暗风） */
    .skeleton-wrap {
      display: flex;
      flex-direction: column;
      gap: 12px;

      .skeleton {
        display: flex;
        gap: 12px;
        align-items: center;
        padding: 12px;
        background: linear-gradient(
          180deg,
          rgba(10, 10, 12, 0.12),
          rgba(6, 6, 8, 0.08)
        );
        border-radius: 12px;
        box-shadow: 0 6px 18px rgba(0, 0, 0, 0.48);
        border: 1px solid rgba($accent-ice, 0.02);
      }

      .sk-avatar {
        width: 44px;
        height: 44px;
        border-radius: 10px;
        background: linear-gradient(90deg, $accent-ice, $ice-core);
        box-shadow: inset 0 -4px 10px rgba(0, 0, 0, 0.28);
      }

      .sk-lines {
        flex: 1;
        .sk-line {
          height: 10px;
          border-radius: 6px;
          background: linear-gradient(
            90deg,
            rgba(255, 255, 255, 0.03),
            rgba(200, 200, 200, 0.02)
          );
          margin-bottom: 8px;
        }
        .sk-line.short {
          width: 40%;
        }
      }
    }
  }

  /* ========== 单条消息卡（散华风） ========== */
  .message-card {
    border-radius: 14px;
    padding: 12px;
    margin: 8px auto;
    width: calc(100% - 48px);
    max-width: 960px;
    border: 1px solid $card-border;
    transition: transform 320ms cubic-bezier(0.2, 0.9, 0.3, 1), box-shadow 320ms,
      border-color 320ms;
    transform-origin: center;
    position: relative;
    z-index: 3;
    overflow: visible;
    background: linear-gradient(
      180deg,
      rgba(10, 12, 16, 0.36),
      rgba(6, 8, 12, 0.48)
    );

    &:hover {
      transform: translateY(-8px) scale(1.01);
      border-color: rgba($accent-ice, 0.06);
      box-shadow: 0 28px 64px rgba(3, 8, 14, 0.72);
    }

    /* 冰锋掠过（hover ephemeral effect） */
    &::after {
      content: "";
      position: absolute;
      left: -6%;
      top: 0;
      width: 0;
      height: 100%;
      transform: skewX(-10deg);
      background: linear-gradient(
        90deg,
        rgba(255, 255, 255, 0),
        rgba($ice-core, 0.6) 30%,
        rgba($accent-ice, 0.18) 60%,
        rgba($scarlet, 0.03) 96%
      );
      opacity: 0;
      pointer-events: none;
      transition: width 420ms cubic-bezier(0.2, 0.9, 0.2, 1), opacity 260ms ease;
    }
    &:hover::after {
      width: 120%;
      opacity: 0.12;
    }

    /* 元信息区域 */
    .message-meta {
      display: flex;
      justify-content: space-between;
      align-items: flex-start;
      gap: 12px;
      margin-bottom: 10px;

      .left-meta {
        display: flex;
        gap: 12px;
        align-items: center;

        .name-avatar {
          width: 48px;
          height: 48px;
          border-radius: 10px;
          display: inline-flex;
          align-items: center;
          justify-content: center;
          font-weight: 900;
          color: $bg-deep;
          background: linear-gradient(
            180deg,
            rgba($accent-ice, 0.42) 0%,
            rgba($accent-ice, 0.76) 60%
          );
          box-shadow: inset 0 -6px 18px rgba(0, 0, 0, 0.35);
          font-size: 16px;
          flex-shrink: 0;
          position: relative;
        }

        .meta-texts {
          .message-name {
            font-size: 15px;
            color: $ice-core;
            font-weight: 800;
            line-height: 1;
            text-shadow: 0 4px 12px rgba(0, 0, 0, 0.45);
          }
          .message-time {
            font-size: 12px;
            color: $text-muted;
            margin-top: 2px;
          }
        }
      } /* left-meta end */

      .right-meta {
        display: flex;
        gap: 8px;
        align-items: center;
      }
    } /* message-meta end */

    /* 正文内层（暗玻璃块） */
    .message-content {
      display: block;
      color: $text-main;
      padding: 14px 16px;
      border-radius: 10px;
      font-size: 16px;
      line-height: 1.85;
      letter-spacing: 0.24px;
      word-break: break-word;
      white-space: pre-wrap;
      margin: 0;
      transition: background 180ms ease, color 120ms ease, transform 120ms ease;
      background: linear-gradient(
        180deg,
        rgba(255, 255, 255, 0.02),
        rgba(255, 255, 255, 0.01)
      );
      box-shadow: inset 0 -6px 18px rgba(0, 0, 0, 0.28);

      a {
        color: $accent-ice;
        text-decoration: underline;
        text-underline-offset: 3px;
        font-weight: 600;
      }

      code,
      pre {
        background: rgba(0, 0, 0, 0.06);
        padding: 4px 8px;
        border-radius: 6px;
        font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, "Roboto Mono",
          monospace;
        font-size: 0.92em;
        color: rgba(220, 240, 255, 0.94);
      }
    }

    @media (max-width: 720px) {
      padding: 10px;
      .message-content {
        padding: 12px;
        font-size: 15px;
        line-height: 1.7;
        border-radius: 8px;
      }
    }
  } /* .message-card end */

  /* ========== 底部输入区（冰霜收口） ========== */
  .message-form {
    position: fixed;
    left: 50%;
    transform: translateX(-50%);
    bottom: 18px;
    width: calc(100% - 32px);
    max-width: 960px;
    background: linear-gradient(
      180deg,
      rgba(8, 12, 16, 0.9),
      rgba(6, 10, 14, 0.92)
    );
    padding: 14px;
    border-radius: 14px;
    display: flex;
    flex-direction: column;
    gap: 10px;
    box-shadow: 0 28px 80px rgba(0, 0, 0, 0.28), inset 0 1px 0 $inner-glow;
    z-index: 6;
    border: 1px solid rgba($accent-ice, 0.02);
    will-change: transform, opacity;

    input,
    textarea {
      padding: 12px 14px;
      border-radius: 12px;
      border: 1px solid rgba($accent-ice, 0.03);
      font-size: 14px;
      outline: none;
      transition: box-shadow 180ms ease, border-color 180ms ease,
        background 180ms ease, transform 120ms ease;
      background: linear-gradient(
        180deg,
        rgba(8, 12, 16, 0.36),
        rgba(6, 8, 12, 0.36)
      );
      box-shadow: inset 0 -4px 10px rgba(0, 0, 0, 0.04);
      color: $text-main;
      resize: none;
      -webkit-appearance: none;
      -moz-appearance: none;
    }

    input::placeholder,
    textarea::placeholder {
      color: rgba($text-main, 0.42);
    }

    input:focus,
    textarea:focus {
      border-color: $accent-ice;
      box-shadow: 0 10px 30px rgba($accent-ice, 0.06),
        inset 0 -6px 12px rgba(0, 0, 0, 0.04);
      transform: translateY(-1px);
      background: linear-gradient(
        180deg,
        rgba(10, 14, 18, 0.48),
        rgba(8, 12, 16, 0.46)
      );
    }

    textarea {
      min-height: 64px;
      max-height: 220px;
      line-height: 1.6;
    }

    .form-row {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 12px;

      .hint {
        color: rgba($text-main, 0.86);
        font-size: 13px;
        display: flex;
        align-items: center;
        gap: 8px;
        kbd {
          background: linear-gradient(
            180deg,
            rgba(255, 255, 255, 0.02),
            rgba(255, 255, 255, 0.01)
          );
          border-radius: 6px;
          padding: 3px 7px;
          border: 1px solid rgba($accent-ice, 0.03);
          font-size: 12px;
          box-shadow: inset 0 -2px 6px rgba(0, 0, 0, 0.04);
          color: $ice-core;
          font-weight: 700;
          letter-spacing: 0.6px;
        }
      }

      button {
        padding: 10px 18px;
        background: linear-gradient(
          180deg,
          $accent-ice 0%,
          rgba($scarlet, 0.06) 60%
        );
        color: #04101a;
        border: none;
        border-radius: 12px;
        cursor: pointer;
        font-weight: 800;
        box-shadow: 0 14px 48px rgba($accent-ice, 0.06),
          inset 0 1px 0 rgba(255, 255, 255, 0.02);
        transition: transform 140ms ease, box-shadow 140ms ease,
          opacity 140ms ease;
        display: inline-flex;
        align-items: center;
        gap: 8px;

        &::after {
          content: "";
          display: inline-block;
          width: 6px;
          height: 6px;
          border-radius: 50%;
          background: radial-gradient(
            circle at 40% 40%,
            rgba($scarlet, 0.8) 0%,
            $accent-ice 40%,
            transparent 60%
          );
          box-shadow: 0 6px 18px rgba($accent-ice, 0.06);
        }

        &:hover {
          transform: translateY(-3px) scale(1.02);
          box-shadow: 0 22px 64px rgba($accent-ice, 0.12);
        }
        &:active {
          transform: translateY(-1px) scale(0.995);
        }
      }

      button:disabled {
        opacity: 0.52;
        cursor: not-allowed;
        transform: none;
        box-shadow: none;
        background: linear-gradient(
          180deg,
          rgba(10, 10, 10, 0.06),
          rgba(8, 8, 8, 0.06)
        );
        color: rgba(220, 240, 255, 0.6);
      }
    }

    &.pulse {
      animation: formPulse 1200ms ease-in-out 1;
    }
  }

  @keyframes formPulse {
    0% {
      transform: translateX(-50%) scale(1);
      box-shadow: 0 28px 80px rgba(0, 0, 0, 0.18);
    }
    40% {
      transform: translateX(-50%) scale(1.01);
      box-shadow: 0 36px 96px rgba($accent-ice, 0.08);
    }
    100% {
      transform: translateX(-50%) scale(1);
      box-shadow: 0 28px 80px rgba(0, 0, 0, 0.18);
    }
  }

  /* 浮动/印章/音符（冷蓝） */
  .floating-note {
    position: absolute;
    font-size: 14px;
    color: $accent-ice;
    opacity: 0.95;
    transform-origin: center;
    animation: note-float 4.6s ease-in-out infinite;
    filter: drop-shadow(0 8px 20px rgba($accent-ice, 0.06));
  }

  @keyframes staff-scroll {
    0% {
      transform: translateY(-6%);
    }
    50% {
      transform: translateY(6%);
    }
    100% {
      transform: translateY(-6%);
    }
  }
  @keyframes note-float {
    0% {
      transform: translateY(0) rotate(-6deg) scale(0.95);
      opacity: 0.85;
    }
    50% {
      transform: translateY(-12px) rotate(3deg) scale(1);
      opacity: 1;
    }
    100% {
      transform: translateY(0) rotate(-6deg) scale(0.95);
      opacity: 0.85;
    }
  }
  @keyframes crane-drift {
    0% {
      transform: translateY(0) rotate(-3deg);
      opacity: 0.08;
    }
    50% {
      transform: translateY(-6px) rotate(0deg);
      opacity: 0.12;
    }
    100% {
      transform: translateY(0) rotate(-3deg);
      opacity: 0.08;
    }
  }

  /* ========== 响应式 ========== */
  @media (max-width: 980px) {
    padding-top: 90px;
    .carousel1 {
      display: none;
    }
    .carousel2 {
      display: block;
    }

    .board-header {
      left: 12px;
      transform: none;
      width: calc(100% - 24px);
    }

    .message-list {
      padding: 18px 12px 260px;
      .message-list-inner {
        gap: 12px;
      }
    }

    .message-card {
      width: calc(100% - 28px);
      border-radius: 12px;
      padding: 12px;
      .name-avatar {
        width: 44px;
        height: 44px;
      }
    }

    .message-form {
      left: 12px;
      transform: none;
      width: calc(100% - 24px);
      bottom: 12px;
      padding: 12px;
    }
  }

  /* 无障碍隐藏 */
  .sr-only {
    position: absolute !important;
    width: 1px !important;
    height: 1px !important;
    padding: 0 !important;
    margin: -1px !important;
    overflow: hidden !important;
    clip: rect(0, 0, 0, 0) !important;
    white-space: nowrap !important;
    border: 0 !important;
  }
}
</style>
