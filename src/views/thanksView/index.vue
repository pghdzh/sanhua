<template>
  <div class="ack-page">
    <div class="ack-container">
      <h1 class="page-title">鸣谢名单</h1>
      <ul class="ack-list">
        <li
          v-for="(item, index) in acknowledgements"
          :key="index"
          class="ack-item"
          :style="{ animationDelay: index * 0.15 + 's' }"
        >
          <span class="nickname">{{ item.nickname }}:</span>

          <span class="suggestion">{{ item.suggestion }}</span>
        </li>
      </ul>
      <div v-if="acknowledgements.length === 0" class="empty">暂无鸣谢内容</div>
    </div>
    <footer class="ack-footer">© 2025 散华电子设定集 · 感谢所有支持者</footer>
  </div>
</template>

<script setup lang="ts">
import { reactive } from "vue";
const acknowledgements = reactive([
  {
    nickname: "雪_霁__",
    suggestion: " 往岁春雷拾往心，今夕朔雪护今朝(人物介绍副标题)",
  },
  {
    nickname: "雪_霁__",
    suggestion: "鬼影侵袭，雪夜轮回，炼狱灼魂铸恶鬼(首页副标题)",
  },
  {
    nickname: "雪_霁__",
    suggestion: "春雷乍响，倒果为因，赤瞳朔雪护天光(首页副标题)",
  },
  {
    nickname: "雪_霁__",
    suggestion: "厨光照脸庞，霜雪映心光，龙包飘香在今朝(首页副标题)",
  },
]);
</script>

<style lang="scss" scoped>
@use "sass:color";

/* ================= 散华配色（变量映射，保持原变量名） ================= */
$bg-deep: #041026; // 冷白底（非纯白，略带蓝）
$deep-2: #0b2432; // 冷雾灰蓝
$accent-1: #5fa6c9; // 冷雾青蓝（主光）
$accent-2: #c84b4b; // 猩红点缀（谨慎使用）
$text-main: #0f1418; // 冷墨黑（高对比）
$silver: #cfeaf7;

$card-bg: rgba(255, 255, 255, 0.94);
$card-border: rgba(16, 26, 34, 0.06);
$glass: rgba(95, 166, 201, 0.04);
$shadow-strong: rgba(6, 8, 12, 0.12);
$shadow-accent: rgba(95, 166, 201, 0.04);

/* 语义变量补充（便于后续微调） */
$muted: rgba($text-main, 0.66);
$accent-glow: rgba($accent-1, 0.1);

/* ================= 整体页面 ================= */
.ack-page {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  padding-top: 64px;
  color: $text-main;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;

  background: radial-gradient(
      circle at 12% 14%,
      rgba($accent-1, 0.03),
      transparent 8%
    ),
    radial-gradient(circle at 78% 70%, rgba($accent-2, 0.02), transparent 10%),
    linear-gradient(135deg, $bg-deep 0%, $deep-2 50%, $bg-deep 100%);
  background-size: 400% 400%;
  animation: bambooGradient 28s ease infinite;
}

/* 容器 */
.ack-container {
  flex: 1;
  padding: 2rem;
  max-width: 720px;
  margin: auto;
  position: relative;
  overflow: visible;
  box-sizing: border-box;
  border-radius: 10px;
}

/* 细微背景位移（竹影 / 冷雾移动感，强度极低，不干扰阅读） */
@keyframes bambooGradient {
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

/* ================= 标题 ================= */
.page-title {
  font-size: 2rem;
  text-align: center;
  margin-bottom: 1.4rem;
  font-weight: 900;
  line-height: 1.05;
  background: linear-gradient(
    90deg,
    $accent-1 0%,
    color.scale($accent-1, $lightness: 36%) 78%
  );
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
  color: transparent;
  text-shadow: 0 6px 18px rgba(5, 8, 12, 0.06);
}

/* 纸纹（覆盖层）——降低不透明度以避免抢眼 */
.ack-container::before {
  content: "";
  position: absolute;
  inset: 0;
  pointer-events: none;
  background-image: repeating-linear-gradient(
      to right,
      rgba(0, 0, 0, 0.004) 0 1px,
      transparent 1px 36px
    ),
    radial-gradient(circle at 18% 20%, rgba($accent-1, 0.02), transparent 8%),
    radial-gradient(circle at 78% 70%, rgba($accent-2, 0.01), transparent 8%);
  mix-blend-mode: multiply;
  opacity: 0.7;
  border-radius: 10px;
}

/* ================= 列表与条目 ================= */
.ack-list {
  list-style: none;
  padding: 0;
  margin: 0;

  .ack-item {
    display: flex;
    align-items: flex-start;
    gap: 0.8rem;
    margin-bottom: 0.9rem;
    padding: 0.85rem 1rem;
    background: linear-gradient(
      180deg,
      rgba(255, 255, 255, 0.96),
      rgba(246, 250, 253, 0.96)
    );
    border-radius: 10px;
    border: 1px solid $card-border;
    box-shadow: 0 8px 22px $shadow-strong, inset 0 0 12px $shadow-accent;
    transition: transform 220ms cubic-bezier(0.2, 0.9, 0.2, 1), box-shadow 220ms,
      background 220ms;
    opacity: 0;
    transform: translateY(8px);
    animation: itemIn 520ms ease forwards;
    line-height: 1.5;
    will-change: transform, box-shadow, background;

    &:hover {
      transform: translateY(-6px);
      box-shadow: 0 22px 48px rgba(6, 8, 12, 0.12),
        inset 0 0 28px rgba($accent-1, 0.02);
      background: linear-gradient(
        180deg,
        rgba(255, 255, 255, 1),
        rgba(250, 252, 254, 1)
      );
    }

    /* 姓名（笔触冷光） */
    .nickname {
      font-weight: 800;
      margin-right: 0.6rem;
      font-size: 0.98rem;
      line-height: 1.25;
      background: linear-gradient(
        90deg,
        $accent-1 0%,
        color.scale($accent-1, $lightness: 30%) 100%
      );
      -webkit-background-clip: text;
      background-clip: text;
      -webkit-text-fill-color: transparent;
      color: transparent;
      text-shadow: 0 0 6px rgba($accent-1, 0.04);
      transition: transform 260ms cubic-bezier(0.2, 0.9, 0.2, 1),
        text-shadow 200ms;
      display: inline-block;
      min-width: 84px;
    }

    /* 主体内容（可读性优化） */
    .suggestion {
      flex: 1;
      color: rgba($text-main, 0.94);
      font-size: 0.98rem;
      line-height: 1.65;
      word-break: break-word;
      white-space: pre-wrap;
      letter-spacing: 0.02em;
    }

    /* 印章（保留点缀，但降低饱和与尺寸） */
    .stamp {
      margin-left: 12px;
      flex-shrink: 0;
      width: 28px;
      height: 28px;
      border-radius: 4px;
      background: linear-gradient(
        135deg,
        rgba($accent-2, 0.98),
        rgba($accent-2, 0.82)
      );
      box-shadow: 0 6px 12px rgba($accent-2, 0.08);
      display: grid;
      place-items: center;
      font-size: 11px;
      font-weight: 900;
      color: #fff;
      transform: translateZ(0);
    }
  }
}

/* 条目进入动画（可配合延迟 JS 添加更雅致的阶梯效果） */
@keyframes itemIn {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 空状态 */
.empty {
  text-align: center;
  color: rgba($text-main, 0.56);
  margin-top: 2rem;
  font-size: 0.98rem;
}

/* 页脚（宣纸浅纹 + 冷边） */
.ack-footer {
  text-align: center;
  padding: 1rem 0;
  color: #dbeefb;
  font-size: 0.9rem;
  margin-top: 1.6rem;
  background: linear-gradient(
    180deg,
    rgba(8, 12, 18, 0.6),
    rgba(120, 208, 255, 0.08)
  );
  border-top: 1px solid rgba($accent-1, 0.02);
  border-radius: 8px;
}

/* 轻风动效（可按需加 .breeze 到容器或单条） */
@keyframes breezeLift {
  0% {
    transform: translateY(0);
    opacity: 0.98;
  }
  50% {
    transform: translateY(-6px);
    opacity: 1;
  }
  100% {
    transform: translateY(0);
    opacity: 0.98;
  }
}
.breeze {
  animation: breezeLift 8s ease-in-out infinite;
}

/* 响应式 */
@media (max-width: 520px) {
  .ack-container {
    padding: 1.25rem;
    max-width: 92%;
  }
  .page-title {
    font-size: 1.6rem;
  }
  .ack-item {
    padding: 0.7rem 0.8rem;
    border-radius: 8px;
  }
  .nickname {
    font-size: 0.95rem;
    min-width: 68px;
  }
  .suggestion {
    font-size: 0.95rem;
    line-height: 1.6;
  }
  .ack-list .ack-item .stamp {
    display: none;
  }
}

/* 鹤影漂移（若插入 svg.crane-shadow） */
@keyframes crane-drift {
  0% {
    transform: translateY(0) rotate(-4deg);
    opacity: 0.06;
  }
  50% {
    transform: translateY(-6px) rotate(0deg);
    opacity: 0.1;
  }
  100% {
    transform: translateY(0) rotate(-4deg);
    opacity: 0.06;
  }
}
</style>
