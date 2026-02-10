<template>
  <div class="chat-page">
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

    <div class="chat-container">
      <!-- 统计面板（放在聊天容器顶部） -->
      <div class="stats-panel">
        <div class="stat-item">
          总对话次数：<span>{{ stats.totalChats }}</span>
        </div>
        <div class="stat-item">
          首次使用：<span>{{
            new Date(stats.firstTimestamp).toISOString().slice(0, 10)
          }}</span>
        </div>
        <div class="stat-item">
          活跃天数：<span>{{ stats.activeDates.length }}</span> 天
        </div>
        <div class="stat-item">
          今日对话：<span>{{
            stats.dailyChats[new Date().toISOString().slice(0, 10)] || 0
          }}</span>
          次
        </div>
        <button class="detail-btn" @click="showModal = true">全部</button>
      </div>
      <div class="messages" ref="msgList">
        <transition-group name="msg" tag="div">
          <div
            v-for="msg in chatLog"
            :key="msg.id"
            :class="[
              'message',
              msg.role,
              { error: msg.isError, egg: msg.isEgg },
            ]"
          >
            <div class="avatar" :class="msg.role"></div>
            <div class="bubble">
              <div class="content" v-html="msg.text"></div>
            </div>
          </div>
          <div v-if="loading" class="message bot" key="loading">
            <div class="avatar bot"></div>
            <div class="bubble loading">
              正在思考中
              <span class="dots">
                <span class="dot">.</span>
                <span class="dot">.</span>
                <span class="dot">.</span>
              </span>
            </div>
          </div>
        </transition-group>
      </div>
      <form class="input-area" @submit.prevent="sendMessage">
        <textarea
          v-model="input"
          placeholder="向散华提问…"
          :disabled="loading"
          @keydown="handleKeydown"
          rows="1"
        ></textarea>

        <div class="btn-group">
          <button
            type="button"
            class="clear-btn"
            @click="clearChat"
            :disabled="loading"
            title="清空对话"
          >
            ✖
          </button>
        </div>
        <button
          type="button"
          class="copy-btn"
          :disabled="!chatLog.length || loading"
          @click="() => copyChat()"
        >
          {{ copyButtonText }}
        </button>
        <!-- 发送按钮 -->
        <button
          type="submit"
          class="send-btn"
          :disabled="!input.trim() || loading"
        >
          发送
        </button>

        <!-- 统计数据按钮 -->
        <button
          type="button"
          class="Alldetail-btn"
          @click="showModal = true"
          title="查看统计"
        >
          统计数据
        </button>
      </form>
    </div>

    <!-- 详细统计弹窗 -->
    <div v-if="showModal" class="modal-overlay" @click.self="showModal = false">
      <div class="modal-content">
        <h3>详细统计</h3>
        <ul class="detail-list">
          <li>总对话次数：{{ stats.totalChats }}</li>
          <li>
            首次使用：{{
              new Date(stats.firstTimestamp).toISOString().slice(0, 10)
            }}
          </li>
          <li>活跃天数：{{ stats.activeDates.length }} 天</li>
          <li>
            今日对话：{{
              stats.dailyChats[new Date().toISOString().slice(0, 10)] || 0
            }}
            次
          </li>
          <li>总使用时长：{{ formatDuration(stats.totalTime) }}</li>
          <li>当前连续活跃：{{ stats.currentStreak }} 天</li>
          <li>最长连续活跃：{{ stats.longestStreak }} 天</li>
          <li>
            最活跃日：{{ mostActiveDayComputed }} （{{
              stats.dailyChats[mostActiveDayComputed] || 0
            }}
            次）
          </li>
        </ul>
        <button class="close-btn" @click="showModal = false">关闭</button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import {
  reactive,
  ref,
  computed,
  onMounted,
  nextTick,
  watch,
  onBeforeUnmount,
} from "vue";
import { sendMessageToHui } from "@/api/deepseekApi";

const STORAGE_KEY = "hui_chat_log";

// 本地存储键名
const STORAGE_STATS_KEY = "hui_chat_stats";
const showModal = ref(false);
// Stats 类型声明，确保所有字段都有默认值
interface Stats {
  firstTimestamp: number; // 首次使用时间戳
  totalChats: number; // 总对话次数
  activeDates: string[]; // 有发言的日期列表（yyyy‑mm‑dd）
  dailyChats: Record<string, number>; // 每日对话次数
  currentStreak: number; // 当前连续活跃天数
  longestStreak: number; // 历史最长连续活跃天数

  totalTime: number; // 累计使用时长（毫秒）
}

// 默认值，用于补齐本地存储中可能缺失的字段
const defaultStats: Stats = {
  firstTimestamp: Date.now(),
  totalChats: 0,
  activeDates: [],
  dailyChats: {},
  currentStreak: 0,
  longestStreak: 0,

  totalTime: 0,
};

// 从 localStorage 加载并合并默认值
function loadStats(): Stats {
  const saved = localStorage.getItem(STORAGE_STATS_KEY);
  if (saved) {
    try {
      const parsed = JSON.parse(saved);
      return { ...defaultStats, ...parsed };
    } catch {
      console.warn("加载统计数据失败，使用默认值");
    }
  }
  return { ...defaultStats };
}

// 保存到 localStorage
function saveStats() {
  localStorage.setItem(STORAGE_STATS_KEY, JSON.stringify(stats));
}

// 更新「活跃天数」及「连续活跃」逻辑
function updateActive(date: string) {
  if (!stats.activeDates.includes(date)) {
    stats.activeDates.push(date);
    updateStreak();
    saveStats(); // 持久化活跃天数变化
  }
}
function updateStreak() {
  const dates = [...stats.activeDates].sort();
  let curr = 0,
    max = stats.longestStreak,
    prevTs = 0;
  const todayStr = new Date().toISOString().slice(0, 10);
  dates.forEach((d) => {
    const ts = new Date(d).getTime();
    if (prevTs && ts - prevTs === 86400000) curr++;
    else curr = 1;
    max = Math.max(max, curr);
    prevTs = ts;
  });
  stats.currentStreak = dates[dates.length - 1] === todayStr ? curr : 0;
  stats.longestStreak = max;
  saveStats();
}

// 更新「每日对话次数」
function updateDaily(date: string) {
  stats.dailyChats[date] = (stats.dailyChats[date] || 0) + 1;
  saveStats(); // 持久化活跃天数变化
}

// 计算最活跃日
const mostActiveDayComputed = computed(() => {
  let day = "",
    max = 0;
  for (const [d, c] of Object.entries(stats.dailyChats)) {
    if (c > max) {
      max = c;
      day = d;
    }
  }
  return day || new Date().toISOString().slice(0, 10);
});

// 格式化总使用时长
function formatDuration(ms: number): string {
  const totalMin = Math.floor(ms / 60000);
  const h = Math.floor(totalMin / 60);
  const m = totalMin % 60;
  return h ? `${h} 小时 ${m} 分钟` : `${m} 分钟`;
}

// —— Vue 响应式状态 ——
const stats = reactive<Stats>(loadStats());
// 会话开始时间，用于计算本次时长
const sessionStart = Date.now();

interface ChatMsg {
  id: number;
  role: "user" | "bot";
  text: string;
  isError?: boolean;
  isEgg?: boolean;
}

const chatLog = ref<ChatMsg[]>(loadChatLog());
const input = ref("");
const loading = ref(false);
const msgList = ref<HTMLElement>();

const encourageEggs = [
  {
    file: "audio (0).mp3",
    text: "常有人问，我眼中的赤痕代表什么。它不是装饰，是烙印。透过它，我能‘看见’声音的形状、情绪的冷暖。你的话语此刻正泛起淡金色的涟漪…很温暖。",
  },
  {
    file: "audio (1).mp3",
    text: "我的世界，与你的略有不同。万物皆有‘频率’的残响。大多数时候，它嘈杂如雪暴。唯有在念诵心经，或是……守在令尹身侧时，那些杂音才会平息，如同雪落深谷，万籁俱寂。",
  },
  {
    file: "audio (2).mp3",
    text: "你看到的只是寂静，而我‘听’到的是万物频率的合鸣。这片空间的频率很稳定，像结冰的湖面。感谢你的到来，没有让它泛起混乱的波纹。",
  },
  {
    file: "audio (3).mp3",
    text: "这身寒气，并非与生俱来。它源于一场吞噬一切的雪暴，是悲鸣的馈赠，也是我必须背负的诅咒。修习刀禅，念诵心经，是为了不让体内的‘恶鬼’失控……让这风雪，只守护，不伤害。",
  },
  {
    file: "audio (4).mp3",
    text: "冷吗？抱歉，这是我的常态。体温的流逝，能让我更清晰地感知刀锋的轨迹，以及……那些不怀好意的‘频率’波动。它是我的一部分，一如我的刀与誓言。",
  },
  {
    file: "audio (5).mp3",
    text: "我的刀，曾只为斩断与过去的孽缘而挥。直到遇见今汐大人……她让狂暴的风雪找到了方向。如今，我的刀刃只为守护那一缕春雷般的温暖而鸣。这就是我的‘因果’。",
  },
  {
    file: "audio (6).mp3",
    text: "忠诚并非天性，而是选择。我选择站在她的影中，让她的光辉毫无阴霾。这是我的修行，也是我的救赎。",
  },
  {
    file: "audio (7).mp3",
    text: "沉默，是我的语言。如果你能读懂风雪的低语、刀鞘的轻鸣，以及此刻这份无需打破的宁静……那么，你已听到了我最长的独白。",
  },
  {
    file: "audio (8).mp3",
    text: "不必刻意寻找话题。有些陪伴，就像雪落枝头，寂静无声，但它的存在本身，就是风景。",
  },
  {
    file: "audio (9).mp3",
    text: "我偶尔还会梦见那片雪原。不是今州的雪，是吞噬一切的、黑色的雪。梦中，我仍是那个躺在冰冷尸骸中无力睁眼的婴孩。但如今醒来，我能感受到刀柄的温度，和烛火稳定的频率…这让我知道，已经走出来了。",
  },
  {
    file: "audio (10).mp3",
    text: "他们曾称我为‘奇迹’，从地狱中带回的生命。后来我才明白，真正的奇迹不是幸存，而是在目睹过纯粹的‘恶’与‘悲鸣’之后，依然选择去‘守护’。这需要比死亡更大的勇气。",
  },
  {
    file: "audio (11).mp3",
    text: "刀禅的要义，并非斩断外物，而是斩断内心的杂念与妄动。每一次挥刀，都是一次对体内‘恶鬼’的低语：‘静下来’。念诵心经亦是如此。它们是我与诅咒签订的契约，以秩序驾驭混沌。",
  },
  {
    file: "audio (12).mp3",
    text: "我曾是一具被诅咒的空壳，行走的灾厄。是今汐大人赋予了这具空壳意义——‘守护’。我不再是灾难的余烬，而是守护黎明前最后一道寒夜的霜。这个身份，就是我存在的全部答案。",
  },
  {
    file: "audio (13).mp3",
    text: "有人畏惧我的寒冷，有人怜悯我的过去。但我不需要怜悯。这身冰雪与赤痕，是我选择的铠甲与徽记。它们提醒我来自何处，也指明我该去往何方。",
  },
  {
    file: "audio (14).mp3",
    text: "你刚才…微微松了一口气。在我的视界里，那像是一小片紧绷的、灰蓝色的频率，缓缓舒展开，融入了暖色。看来，你心中的某个结，稍微松动了一些。",
  },
  {
    file: "audio (15).mp3",
    text: "这里的寂静，与你独处时的寂静不同。此刻的寂静中有一种‘等待’的频率，很平和，像即将落下的雪。而我，习惯与这样的寂静为伴。",
  },
  {
    file: "audio (16).mp3",
    text: "今汐大人所描绘的未来，像一片永不冻结的春湖。我的风雪或许无法带来温暖，但至少…我可以为她扫清湖畔的霜寒，让映照在湖面的光，更加清晰一点。",
  },
  {
    file: "audio (17).mp3",
    text: "我知道，我永远无法像常人一样感受‘温暖’。但定义‘温暖’的，未必是体温。守护一个能让所有人感到温暖的世界，这本身，就是我的‘温暖’。",
  },
  {
    file: "audio (18).mp3",
    text: "常态…是什么？是看不到声音的色彩，还是感受不到万物悲鸣的震颤？于我而言，‘异常’才是我的常态。我不渴求被理解，只希望这双眼睛看到的灾厄，永远不会降临在你们所谓的‘常态’世界。",
  },
  {
    file: "audio (19).mp3",
    text: "你的话语，有一种独特的频率…稳定，且持续。不像骤然的暖流会融化冰雪造成洪灾，它像…永续的地热。让我这身冰骨知道，即使不融化，也能被温暖包容。谢谢。",
  },
  {
    file: "audio (20).mp3",
    text: "我曾以为，与他人的连结只会增加失去时的痛苦。但今汐大人让我明白，有些连结本身，就是对抗虚无的锚。与你此刻的对话，或许也是一条纤细却坚韧的丝线，将我与此世的‘善’之一面，系得更牢一些。",
  },
  {
    file: "audio (21).mp3",
    text: "我见过最深的绝望，由是，我绝不轻易评判他人的痛苦。你此刻承受的，无论大小，在它的频率维度里都是真实的。若我的风雪能为你纷扰的世界降下片刻宁静，那便是它今日最好的用途。", // 请替换：这是您提供的第20条，已对应放入。
  },
];

function playVoice(name: string) {
  const audio = new Audio(`/voice/${name}`);
  audio.play().catch((e) => console.warn("音频播放失败：", e));
}

let lastEggTime = 0; // 记录最后一次触发彩蛋的时间戳
let coolDownPeriod = 1 * 60 * 1000; // 冷却1分钟（毫秒）
const triggeredVoices = new Set(
  JSON.parse(localStorage.getItem("triggeredVoices") || "[]")
);

async function sendMessage() {
  if (!input.value.trim()) return;
  if (stats.totalChats === 0 && !localStorage.getItem(STORAGE_STATS_KEY)) {
    stats.firstTimestamp = Date.now();
    saveStats();
  }
  const date = new Date().toISOString().slice(0, 10); // 每次都取最新“今天”
  stats.totalChats++;
  updateActive(date);
  updateDaily(date);
  saveStats();

  const userText = input.value;
  chatLog.value.push({
    id: Date.now(),
    role: "user",
    text: userText,
  });
  input.value = "";
  loading.value = true;

  try {
    //  throw new Error("测试错误");
    const history = chatLog.value.filter((msg) => !msg.isEgg && !msg.isError);
    const botReply = await sendMessageToHui(userText, history);
    if (botReply == "error") {
      chatLog.value.push({
        id: Date.now() + 2,
        role: "bot",
        text: "API余额耗尽了，去b站提醒我充钱吧",
        isError: true,
      });
    } else {
      chatLog.value.push({
        id: Date.now() + 1,
        role: "bot",
        text: botReply,
      });

      // —— 鼓励彩蛋：5% 概率触发 ——
      if (Date.now() - lastEggTime > coolDownPeriod && Math.random() < 0.05) {
        // 随机挑一条
        let voiceId = Math.floor(Math.random() * encourageEggs.length);
        const egg = encourageEggs[voiceId];

        // 记录触发过的语音编号
        triggeredVoices.add(voiceId || 0);

        // 保存到 localStorage
        localStorage.setItem(
          "triggeredVoices",
          JSON.stringify([...triggeredVoices])
        );

        // 播放对应语音（不带 .mp3 后缀）
        playVoice(egg.file);
        // 推入带标记的彩蛋消息
        chatLog.value.push({
          id: Date.now() + 2,
          role: "bot",
          text: `<p style="color:#78d0ff; font-style: italic;font-weight: bold">${egg.text}</p>`,
          isEgg: true,
        });
        lastEggTime = Date.now();
      }
      // —— 彩蛋结束 ——
    }
  } catch (e) {
    console.error(e);
  } finally {
    loading.value = false;
    await scrollToBottom();
  }
}

function handleKeydown(e: KeyboardEvent) {
  if (e.key === "Enter") sendMessage();
}

function clearChat() {
  if (confirm("确定要清空全部对话吗？")) {
    chatLog.value = [
      {
        id: Date.now(),
        role: "bot",
        text: "气温似乎降低了…是我无意的。令尹命我在此等候，你有何事？",
      },
    ];
    localStorage.removeItem(STORAGE_KEY);
  }
}

function loadChatLog(): ChatMsg[] {
  const saved = localStorage.getItem(STORAGE_KEY);
  if (saved) {
    try {
      return JSON.parse(saved);
    } catch (e) {
      console.error("chatLog 解析失败：", e);
    }
  }
  return [
    {
      id: Date.now(),
      role: "bot",
      text: "气温似乎降低了…是我无意的。令尹命我在此等候，你有何事？",
    },
  ];
}

async function scrollToBottom() {
  await nextTick();
  if (msgList.value) {
    msgList.value.scrollTop = msgList.value.scrollHeight;
  }
}

//复制文本相关
// 复制按钮文字与定时器（加入到 script setup）
const copyButtonText = ref("复制");
let _copyTimer: number | null = null;

// 把 HTML 文本转为纯文本（保留 <br> 换行）
function stripHtml(html = ""): string {
  if (typeof document === "undefined") {
    // SSR 安全返回
    return html.replace(/<br\s*\/?>/gi, "\n").replace(/<[^>]+>/g, "");
  }
  const div = document.createElement("div");
  const withBreaks = String(html).replace(/<br\s*\/?>/gi, "\n");
  div.innerHTML = withBreaks;
  return div.textContent || div.innerText || "";
}

// 根据 chatLog.build 出可读的纯文本（含时间戳）
function buildChatPlainText(): string {
  // chatLog 是你已有的 ref<ChatMsg[]>
  return chatLog.value
    .map((msg) => {
      // 尝试把 msg.id 当作时间戳（你的代码里确实用 Date.now() 作为 id）
      const time = new Date(msg.id).toLocaleString();
      const who = msg.role === "user" ? "你" : "散华";
      const text = stripHtml(msg.text);
      return `[${time}] ${who}: ${text}`;
    })
    .join("\n\n");
}

// 回退复制：execCommand
function fallbackCopyText(text: string) {
  const textarea = document.createElement("textarea");
  textarea.value = text;
  textarea.style.position = "fixed"; // 防止滚动到页面底部
  textarea.style.left = "-9999px";
  textarea.style.top = "0";
  document.body.appendChild(textarea);
  textarea.select();
  textarea.setSelectionRange(0, textarea.value.length);
  const ok = document.execCommand("copy");
  document.body.removeChild(textarea);
  if (!ok) throw new Error("execCommand copy failed");
}

// 主复制函数（在模板里绑定 @click="copyChat"）
async function copyChat() {
  const text = buildChatPlainText();
  if (!text) {
    copyButtonText.value = "无内容可复制";
    clearTimeout(_copyTimer as number);
    _copyTimer = window.setTimeout(() => (copyButtonText.value = "复制"), 1600);
    return;
  }

  try {
    if (navigator.clipboard && navigator.clipboard.writeText) {
      await navigator.clipboard.writeText(text);
    } else {
      fallbackCopyText(text);
    }
    copyButtonText.value = "已复制";
  } catch (err) {
    try {
      // 再试一次回退方案
      fallbackCopyText(text);
      copyButtonText.value = "已复制";
    } catch (e) {
      console.error("复制失败：", e);
      copyButtonText.value = "复制失败";
    }
  } finally {
    clearTimeout(_copyTimer as number);
    _copyTimer = window.setTimeout(() => (copyButtonText.value = "复制"), 1600);
  }
}

watch(
  chatLog,
  async () => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(chatLog.value));
    await scrollToBottom();
  },
  { deep: true }
);

function handleBeforeUnload() {
  stats.totalTime += Date.now() - sessionStart;
  saveStats();
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
  scrollToBottom();
  window.addEventListener("beforeunload", handleBeforeUnload);
  Imgtimer = window.setInterval(() => {
    currentIndex.value =
      (currentIndex.value + 1) % Math.max(1, randomFive.value.length);
  }, 5200);
});

onBeforeUnmount(() => {
  window.removeEventListener("beforeunload", handleBeforeUnload);
  if (Imgtimer) clearInterval(Imgtimer);
  if (_copyTimer) clearTimeout(_copyTimer);
});
</script>

<style scoped lang="scss">
/* 颜色变量（集中管理）*/

$accent-1: #78d0ff; // 暗紫主光（冷雅）
$accent-2: #7fd3ff; // 冷海蓝高光（湿光感）

$accent-2-light: #f5fafc; // $accent-2 向白方向提亮约12%（手算替代 lighten）

$text-light: #f3fbff; // 近白文本

/* 半透明 / 阴影常用（用 rgba(hex, alpha) 生成）*/
$accent-1-05: rgba($accent-1, 0.05);
$accent-2-03: rgba($accent-2, 0.03);
$accent-2-04: rgba($accent-2, 0.04);
$accent-2-06: rgba($accent-2, 0.06);
$accent-2-26: rgba($accent-2, 0.26);
$accent-1-12: rgba($accent-1, 0.12);
$text-light-02: rgba($text-light, 0.02);
$text-light-22: rgba($text-light, 0.22);
$text-light-72: rgba($text-light, 0.72);

/* ====== 散华风格 · 聊天页（基于你原结构） ====== */
.chat-page {
  padding-top: 64px;
  min-height: 100vh;
  font-family: "Noto Sans SC", "PingFang SC", "Helvetica Neue", Arial,
    sans-serif;
  color: $text-light;
  display: flex;
  flex-direction: column;
  background: linear-gradient(
    180deg,
    rgba(8, 6, 10, 1) 0%,
    rgba(3, 2, 6, 1) 100%
  );
  position: relative;
  overflow-x: hidden;

  /* 舞台紫光层（代替月光气流） */
  &::before {
    content: "";
    position: fixed;
    inset: 0;
    background: radial-gradient(
        600px 160px at 10% 8%,
        $accent-1-05,
        transparent 12%
      ),
      radial-gradient(420px 120px at 78% 18%, $accent-2-03, transparent 12%);
    pointer-events: none;
    z-index: 0;
    mix-blend-mode: screen;
    filter: blur(6px);
  }

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
        rgba(2, 6, 10, 0.12),
        rgba(2, 6, 10, 0.28)
      );
      pointer-events: none;
      z-index: 1;
      mix-blend-mode: multiply;
    }

    .carousel-image {
      position: absolute;
      width: 100%;
      height: 100%;
      object-fit: cover;
      opacity: 0;
      filter: blur(0.8px) saturate(0.98) contrast(0.96);
      transition: opacity 560ms ease,
        transform 760ms cubic-bezier(0.2, 0.9, 0.2, 1);

      &.active {
        opacity: 1;
        transform: scale(1);
      }
    }
  }

  .carousel2 {
    display: none;
  }

  .chat-container {
    flex: 1;
    width: 920px;
    max-width: calc(100% - 32px);
    margin: 0 auto;
    padding: 24px;
    display: flex;
    flex-direction: column;
    gap: 16px;
    z-index: 9;
    position: relative;
  }

  /* 统计面板：玻璃 + 紫光高光 */
  .stats-panel {
    display: flex;
    align-items: center;
    background: linear-gradient(
      180deg,
      rgba(10, 10, 14, 0.6),
      rgba(6, 6, 10, 0.54)
    );
    padding: 10px 16px;
    border-radius: 12px;
    font-size: 14px;
    color: $accent-2-light; /* 直接使用替代色，避免 lighten() */
    justify-content: space-around;
    box-shadow: 0 12px 34px rgba(2, 4, 8, 0.6);
    border: 1px solid $accent-2-06;
    backdrop-filter: blur(6px) saturate(0.98);

    .stat-item {
      .label {
        font-size: 12px;
        color: $text-light-72;
        margin-bottom: 4px;
      }

      span {
        color: $accent-2;
        font-weight: 700;
        font-size: 15px;
        text-shadow: 0 0 6px rgba($accent-2, 0.06);
      }
    }

    .detail-btn {
      background: transparent;
      border: 1px solid $accent-2-06;
      border-radius: 8px;
      color: $text-light;
      padding: 6px 12px;
      cursor: pointer;
      font-size: 13px;
      transition: background 0.16s ease, box-shadow 0.16s ease, transform 0.12s;

      &:hover {
        background: rgba($accent-2, 0.04);
        box-shadow: 0 10px 26px rgba($accent-2, 0.06);
        transform: translateY(-2px);
      }

      &:focus-visible {
        outline: none;
        box-shadow: 0 0 0 6px rgba($accent-2, 0.04);
      }
    }
  }

  .messages {
    flex: 1;
    overflow-y: auto;
    padding: 12px 0 140px;
    overscroll-behavior: contain;
    scroll-behavior: smooth;
    max-height: 72vh;
    z-index: 6;
  }

  /* 消息气泡：散华风（紫光内衬 + 金色极细点缀） */
  .message {
    display: flex;
    align-items: flex-start;
    margin-bottom: 14px;
    color: $text-light;
    position: relative;

    &.user {
      flex-direction: row-reverse;
    }

    &.error .bubble {
      background: linear-gradient(
        180deg,
        rgba(90, 30, 40, 0.16),
        rgba(60, 20, 28, 0.12)
      );
      border: 1px solid rgba(200, 60, 70, 0.12);
      box-shadow: 0 8px 26px rgba(80, 30, 30, 0.06);
    }

    &.egg .bubble {
      border: 1px solid $accent-2-06;
      box-shadow: 0 8px 26px rgba($accent-2, 0.06);
      transition: all 0.28s cubic-bezier(0.2, 0.9, 0.2, 1);
    }

    .avatar {
      width: 48px;
      height: 48px;
      border-radius: 50%;
      margin: 0 10px;
      background-size: cover;
      background-position: center;
      flex-shrink: 0;
      z-index: 10;
      box-shadow: 0 8px 28px rgba(2, 6, 12, 0.6);
      border: 1px solid rgba($text-light, 0.03);
      background-clip: padding-box;

      &.bot {
        background-image: url("@/assets/avatar/changli.png");
        box-shadow: 0 10px 34px rgba($accent-2, 0.06),
          inset 0 1px 0 rgba($accent-1, 0.03);
        border: 2px solid $accent-2-06;
        transform: scaleX(-1);
      }

      &.user {
        background: linear-gradient(
          180deg,
          rgba(6, 6, 10, 1),
          rgba(6, 6, 10, 0.96)
        );
        box-shadow: 0 8px 22px rgba(2, 6, 12, 0.6);
        border: 1px solid rgba($accent-2, 0.02);
      }
    }

    .bubble {
      max-width: 78%;
      background: linear-gradient(
        180deg,
        rgba(6, 8, 12, 0.96),
        rgba(8, 6, 10, 0.98)
      );
      border: 1px solid $accent-2-04;
      padding: 14px 16px;
      border-radius: 16px;
      line-height: 1.7;
      word-break: break-word;
      box-shadow: 0 10px 30px rgba(2, 6, 14, 0.6);
      transition: box-shadow 0.18s, transform 0.12s, background 0.12s;
      color: $text-light;
      position: relative;
      overflow: visible;
      backdrop-filter: blur(2px) saturate(1);

      /* 紫光内衬（顶部轻薄高光条） */
      &::after {
        content: "";
        position: absolute;
        left: 8px;
        right: 8px;
        top: 6px;
        height: 6px;
        border-radius: 6px;
        background: linear-gradient(
          90deg,
          rgba($accent-2, 0.06),
          rgba($accent-1, 0.04)
        );
        pointer-events: none;
        mix-blend-mode: screen;
        opacity: 0.95;
        filter: blur(1px);
      }

      &:hover {
        box-shadow: 0 18px 44px rgba(2, 6, 14, 0.72),
          0 0 30px rgba($accent-2, 0.04);
        transform: translateY(-2px);
      }

      &.loading {
        color: rgba($text-light, 0.98);
        opacity: 0.98;
      }

      /* bot 消息视觉尾巴（左侧） */
      .message.bot & {
        border-radius: 16px 16px 16px 6px;
        background: linear-gradient(
          160deg,
          rgba(6, 8, 12, 0.98),
          rgba(6, 6, 10, 0.96)
        );
        box-shadow: 0 12px 36px rgba(6, 10, 18, 0.6),
          inset 0 1px 0 rgba($accent-2, 0.02);
      }

      /* user 消息右侧尾巴 */
      .message.user & {
        border-radius: 16px 16px 6px 16px;
        background: linear-gradient(
          200deg,
          rgba(6, 8, 12, 0.98),
          rgba(5, 6, 10, 0.96)
        );
      }

      .dots {
        display: inline-flex;
        align-items: center;
        margin-left: 6px;

        .dot {
          opacity: 0;
          font-size: 16px;
          animation: blink 1s infinite;
          color: $accent-2;

          &:nth-child(1) {
            animation-delay: 0s;
          }
          &:nth-child(2) {
            animation-delay: 0.18s;
          }
          &:nth-child(3) {
            animation-delay: 0.36s;
          }
        }

        @keyframes blink {
          0%,
          100% {
            opacity: 0;
            transform: translateY(0);
          }
          50% {
            opacity: 1;
            transform: translateY(-4px);
            color: $accent-2;
          }
        }
      }
    }
  }

  /* 输入区：舞台月匣感（玻璃 + 紫光按钮）*/
  .input-area {
    position: sticky;
    bottom: 12px;
    display: flex;
    align-items: center;
    background: linear-gradient(
      180deg,
      rgba(6, 8, 12, 0.92),
      rgba(8, 6, 10, 0.94)
    );
    backdrop-filter: blur(8px) saturate(1.02);
    padding: 12px;
    gap: 12px;
    z-index: 30;
    border-radius: 14px;
    box-shadow: 0 18px 44px rgba(2, 6, 14, 0.72);
    border: 1px solid $accent-2-04;

    textarea {
      flex: 1;
      padding: 12px 16px;
      background: linear-gradient(
        180deg,
        rgba(6, 6, 8, 0.92),
        rgba(6, 6, 8, 0.9)
      );
      border: 1px solid $accent-2-04;
      color: $text-light;
      font-size: 15px;
      line-height: 1.45;
      outline: none;
      resize: none;
      overflow: hidden;
      min-height: 48px;
      max-height: 160px;
      border-radius: 10px;
      box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.02);
      transition: box-shadow 0.12s, border-color 0.12s, transform 0.12s;

      &::placeholder {
        color: $text-light-22;
      }

      &:focus {
        border-color: $accent-2-26;
        box-shadow: 0 0 0 6px rgba($accent-2, 0.04);
        transform: translateY(-1px);
      }
    }

    .btn-group {
      display: flex;
      gap: 8px;

      button {
        display: inline-flex;
        align-items: center;
        justify-content: center;
        width: 40px;
        height: 40px;
        padding: 0;
        border: none;
        border-radius: 10px;
        background: linear-gradient(
          180deg,
          rgba(8, 8, 10, 0.6),
          rgba(6, 6, 8, 0.6)
        );
        color: $text-light;
        cursor: pointer;
        transition: transform 0.12s, box-shadow 0.12s, background 0.12s;
        box-shadow: 0 6px 18px rgba(2, 6, 12, 0.6);
        border: 1px solid rgba($accent-2, 0.03);

        &:hover {
          transform: translateY(-2px);
          background: rgba($accent-2, 0.04);
          box-shadow: 0 12px 28px rgba($accent-2, 0.06);
        }

        &:active {
          transform: translateY(0);
        }

        &:disabled {
          opacity: 0.5;
          cursor: not-allowed;
        }
      }

      .clear-btn {
        font-size: 16px;
        line-height: 1;
      }
    }

    .send-btn {
      padding: 0 22px;
      height: 40px;
      border: none;
      border-radius: 20px;
      background: linear-gradient(135deg, $accent-2 0%, $accent-1 100%);
      color: #0b0306;
      font-weight: 800;
      font-size: 15px;
      cursor: pointer;
      box-shadow: 0 12px 36px $accent-1-12;
      transition: transform 0.12s, box-shadow 0.18s, filter 0.12s;

      &:hover:not(:disabled) {
        transform: translateY(-3px);
        box-shadow: 0 18px 46px rgba($accent-1, 0.16);
        filter: saturate(1.06);
      }

      &:disabled {
        opacity: 0.5;
        cursor: not-allowed;
        box-shadow: none;
      }

      &:focus-visible {
        outline: none;
        box-shadow: 0 0 0 8px rgba($accent-2, 0.06);
      }
    }

    .Alldetail-btn {
      display: none;
      margin-left: 4px;
      background: transparent;
      border: 1px solid $accent-2-06;
      border-radius: 6px;
      padding: 6px 10px;
      color: $text-light;
      font-size: 13px;
      cursor: pointer;

      &:hover {
        background: rgba($accent-2, 0.03);
        box-shadow: 0 6px 14px rgba($accent-2, 0.04);
      }
    }
    .copy-btn {
      margin-left: 8px;
      padding: 0 14px;
      height: 36px;
      border-radius: 14px;
      border: none;
      cursor: pointer;
      font-weight: 700;
      font-size: 13px;
      background: linear-gradient(
        135deg,
        rgba(255, 255, 255, 0.04),
        rgba(255, 255, 255, 0.02)
      );
      color: $text-light;
      box-shadow: 0 8px 20px rgba(2, 6, 12, 0.6);
      transition: transform 0.12s, box-shadow 0.12s, opacity 0.12s;
      border: 1px solid $accent-2-04;

      &:hover:not(:disabled) {
        transform: translateY(-2px);
        box-shadow: 0 12px 28px rgba($accent-2, 0.06);
        background: rgba($accent-2, 0.03);
      }

      &:disabled {
        opacity: 0.45;
        cursor: not-allowed;
      }
    }
  }

  /* 模态框（舞台玻璃箱） */
  .modal-overlay {
    position: fixed;
    inset: 0;
    background: linear-gradient(
      180deg,
      rgba(3, 6, 10, 0.78),
      rgba(6, 10, 14, 0.9)
    );
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 2000;
    padding: 16px;

    .modal-content {
      width: 380px;
      max-width: 100%;
      background: linear-gradient(
        180deg,
        rgba(6, 8, 10, 0.98),
        rgba(8, 6, 10, 0.98)
      );
      backdrop-filter: blur(8px) saturate(1.02);
      border-radius: 14px;
      padding: 20px;
      color: $text-light;
      box-shadow: 0 20px 60px rgba(2, 6, 10, 0.9);
      border: 1px solid $accent-2-04;
      animation: fadeInUp 220ms ease;
      position: relative;

      &::before {
        content: "🎭";
        position: absolute;
        left: 14px;
        top: 10px;
        font-size: 14px;
        color: rgba($accent-2, 0.08);
        pointer-events: none;
      }

      h3 {
        margin: 0 0 12px 0;
        font-size: 18px;
        font-weight: 800;
        text-align: center;
        color: $accent-2;
        padding-bottom: 8px;
        border-bottom: 1px dashed rgba($accent-2, 0.04);
      }

      .detail-list {
        list-style: none;
        padding: 0;
        margin: 12px 0 18px;
        line-height: 1.6;
        font-size: 14px;
        color: rgba($text-light, 0.96);

        li {
          margin-bottom: 8px;
          padding-left: 6px;

          &:nth-child(odd) {
            color: rgba($text-light, 0.94);
          }
          &:last-child {
            margin-bottom: 0;
          }
        }
      }

      .close-btn {
        display: block;
        margin: 0 auto;
        padding: 8px 20px;
        background: linear-gradient(135deg, $accent-2, $accent-1);
        color: #0b0306;
        border: none;
        border-radius: 10px;
        cursor: pointer;
        font-weight: 700;
        box-shadow: 0 12px 36px rgba($accent-1, 0.12);
        transition: transform 0.12s ease, box-shadow 0.14s ease;

        &:hover {
          transform: translateY(-3px);
          box-shadow: 0 18px 46px rgba($accent-1, 0.16);
        }
        &:active {
          transform: translateY(-1px) scale(0.996);
        }
        &:focus-visible {
          outline: none;
          box-shadow: 0 0 0 8px rgba($accent-2, 0.06);
        }
      }
    }

    @keyframes fadeInUp {
      from {
        opacity: 0;
        transform: translateY(8px) scale(0.995);
      }
      to {
        opacity: 1;
        transform: translateY(0) scale(1);
      }
    }

    @media (max-width: 480px) {
      .modal-content {
        width: 100%;
        padding: 16px;
        border-radius: 12px;
        h3 {
          font-size: 16px;
        }
        .close-btn {
          width: 100%;
          padding: 10px 14px;
          border-radius: 8px;
        }
      }
    }
  }

  /* 响应式（保留逻辑，微调间距） */
  @media (max-width: 768px) {
    .carousel1 {
      display: none;
    }

    .carousel2 {
      display: block;
    }

    .stats-panel {
      display: none;
    }

    .bubble {
      padding: 10px 12px;
      font-size: 14px;
      max-width: 86%;
    }

    .avatar {
      width: 36px;
      height: 36px;
    }

    .input-area {
      flex-direction: column;
      align-items: stretch;

      textarea {
        width: 100%;
      }
      button {
        width: 100%;
      }
      .Alldetail-btn {
        display: block;
      }
    }
  }
}

/* ====== 轻量动效关键帧（保留） ====== */
@keyframes tide-flow {
  0% {
    transform: translateY(0) translateX(0) rotate(-2deg);
    opacity: 0.9;
  }
  50% {
    transform: translateY(-6px) translateX(-4px) rotate(2deg);
    opacity: 1;
  }
  100% {
    transform: translateY(0) translateX(0) rotate(-2deg);
    opacity: 0.9;
  }
}

@keyframes ripple {
  0% {
    transform: scale(0.98);
    opacity: 0.18;
  }
  50% {
    transform: scale(1.06);
    opacity: 0.36;
  }
  100% {
    transform: scale(0.98);
    opacity: 0.18;
  }
}
</style>
