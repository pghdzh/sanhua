<template>
  <div class="megumi-page" aria-live="polite">
    <main class="container" ref="mainContainer">
      <!-- HERO 区域 -->
      <header class="hero" role="banner">
        <div class="left-col">
          <button class="avatar-card">
            <picture class="avatar-wrap">
              <img
                src="@/assets/images1/sanhua-slty (11).webp"
                class="avatar"
                loading="lazy"
                decoding="async"
              />
            </picture>
            <div class="avatar-meta">
              <span class="avatar-badge">共鸣能力：雪舞芳华</span>
            </div>
          </button>
        </div>
        <div class="right-col" ref="metaCard">
          <div class="hero-panel">
            <h1 class="name">
              散华
              <span class="jp">（隶属于今州边庭的令尹近卫）</span>
            </h1>
            <p class="subtitle"> 往岁春雷拾往心，今夕朔雪护今朝</p>
            <p class="lead">
              曾是带来灾难、可洞穿世间万物的修罗，如今将肆虐风雪化为守护的力量，成为了寡言的守护者。
            </p>
          </div>
        </div>
      </header>

      <section id="resonance-status" class="card overview">
        <div class="card-head">
          <h2>共鸣状况</h2>
        </div>
        <div class="two-col">
          <div class="text-block">
            <p>
              共鸣前主诉遭遇海蚀异象，天生眼球结构异常，无视力，在异象大雪中被残象侵蚀右眼频率，产生异常视觉，同时伴随异能觉醒。<br />
              对象声痕位于右眼瞳孔，仅在使用共鸣能力时显现。据对象阐述及鉴定结果佐证，共鸣后部分体征产生异化，右瞳声痕处频率反应混沌无序，呈赤红色，该频率侵蚀反应持续蔓延中，该侵蚀反应具体表现为对象左瞳已由灰转红。<br />
              对象拥有操纵风雪及异常视界的共鸣能力，气象剧烈程度与对象精神集中力呈正相关，结合主诉经历，初步判断其所观测到的视界与常人不同，能直接观测到事物频率。共鸣能力测试结论：频率观测准确率高达99.64%。<br />
              共鸣频谱检验图与已知自然存在的风雪气象频率波动区间相接近；瞳孔局部共鸣频谱检验图结果与已知残象频率波动区间接近。测试均有较强谐振反应，无法完全确认其共鸣源。<br />
              受试样本拉贝尔曲线不具收敛性，中段出现陡升变化，检测结果判断为突变型共鸣者。
            </p>
          </div>
        </div>
      </section>

      <!-- 新增三大主题区域 -->
      <section id="food" class="card special-section">
        <div class="card-head">
          <h2>常界</h2>
        </div>
        <div class="content two-col">
          <div class="img-area">
            <img src="./img/item1.png" loading="lazy" decoding="async" />
          </div>
          <div class="text-block">
            <p>
              散华离开寺庙时，住持赠予散华的项链。<br />
              其蕴含着住持对散华的劝诫与祝福，散华十分珍惜，从未摘取。偶尔心神不定时，散华便会不由自主地摩挲“常界”，同念诵心经一般，它拥有能让散华静下心来的能力。
            </p>
          </div>
        </div>
      </section>
      <section id="shattered-crystal" class="card special-section">
        <div class="card-head">
          <h2>无相</h2>
        </div>
        <div class="content two-col">
          <div class="img-area">
            <img src="./img/item2.png" loading="lazy" decoding="async" />
          </div>
          <div class="text-block">
            <p>
              今汐想要看到散华眼中的世界，散华便去向瑝珑一位有名的皮影戏大师求教，以这种形式向今汐展示她所见到的地狱一隅。可这个光怪陆离的世界却意外得到今汐的喜爱，这让散华偶尔会产生，自己也能喜欢上这个地狱的错觉。<br />
              与粗犷的怪物皮影不同，另有一对精致的人形皮影为散华私藏之物。
            </p>
          </div>
        </div>
      </section>

      <section id="cyclic-clock" class="card special-section">
        <div class="card-head">
          <h2>刹那</h2>
        </div>
        <div class="content two-col">
          <div class="img-area">
            <img src="./img/item3.png" loading="lazy" decoding="async" />
          </div>
          <div class="text-block">
            <p>
              散华曾认为“终将消逝”是一切美丽事物之所以美丽的根源，就如同冰雪如此洁净美丽，最终却总会消融。<br />
              但偶尔……当她在掌心凝结出一片冰雪，望其出神之时，她是在感叹这刹那的美丽，还是在为终将到来的消逝而伤感？<br />
              散华将这霜花冻存起来。正如此刻，她已拥有想要永远铭记的刹那，以及决心保护的美丽。
            </p>
          </div>
        </div>
      </section>

      <section id="collective-album" class="card special-section">
        <div class="card-head">
          <h2>剔透玲珑包</h2>
        </div>
        <div class="content two-col">
          <div class="img-area">
            <img src="./img/food.png" loading="lazy" decoding="async" />
          </div>
          <div class="text-block">
            <p>
              在小龙包的制作基础上替换了部分原材料，散华将其制作为外形晶莹剔透、口感冰凉清爽的降暑甜品。炎热酷暑时，总能在边庭内殿的桌上看见这道甜点，深受各界来访人士......以及今州令尹的喜爱。
            </p>
          </div>
        </div>
      </section>

      <section
        id="timeline"
        class="card timeline"
        aria-labelledby="timeline-heading"
      >
        <div class="card-head">
          <h2 id="timeline-heading">共鸣链详解</h2>
        </div>
        <div class="tl-wrap" role="list">
          <div class="vl" aria-hidden="true"></div>
          <ol class="tl-list">
            <li
              v-for="(ev, i) in timeline"
              :key="i"
              class="tl-item"
              role="listitem"
              tabindex="0"
              :aria-label="``"
            >
              <div class="tl-card">
                <h3 class="tl-title">第 {{ i + 1 }} 链 · {{ ev.title }}</h3>
                <p class="tl-desc">{{ ev.desc }}</p>
              </div>
            </li>
          </ol>
        </div>
      </section>

      <section class="ll-stories" ref="storyRef">
        <div class="card" role="region" aria-labelledby="stories-title">
          <header class="card-head">
            <h2 id="stories-title" class="card-title">散华纪事</h2>
          </header>

          <main class="card-body">
            <transition name="fade-slide" mode="out-in">
              <article
                :key="items[current].id"
                :id="`panel-${items[current].id}`"
                class="story"
                role="tabpanel"
                :aria-labelledby="`tab-${items[current].id}`"
              >
                <h3 class="story-head">{{ items[current].title }}</h3>
                <div
                  class="story-content"
                  v-html="items[current].content"
                ></div>
              </article>
            </transition>
          </main>

          <footer class="card-foot">
            <button class="nav-btn" @click="prev" aria-label="上一则故事">
              ‹
            </button>
            <div class="pager">{{ current + 1 }} / {{ items.length }}</div>
            <button class="nav-btn" @click="next" aria-label="下一则故事">
              ›
            </button>
          </footer>
        </div>
      </section>
    </main>
    <!-- <div class="floating-chibis">
      <img
        v-for="(pet, i) in chibiList"
        :key="i"
        :src="pet.src"
        :style="{ top: pet.top + 'px', left: pet.left + 'px' }"
        class="chibi-img"
      />
    </div> -->
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, nextTick } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
gsap.registerPlugin(ScrollTrigger);

const timeline = ref([
  {
    title: "孤身孑然",
    desc: `施放第5段普攻时，散华自身暴击提升15%，持续10秒。。`,
  },
  {
    title: "净雪明心",
    desc: `重击·爆裂消耗的耐力下降10点，施放共鸣技能·朔雪永冻时散华抗打断能力提升，持续5秒。`,
  },
  {
    title: "目视异常",
    desc: `散华攻击生命低于70%的目标时，造成的伤害提升35%。`,
  },
  {
    title: "剑修五蕴",
    desc: `施放共鸣解放·焦瞑冻土时，回复10点共鸣能量。
并且5秒内的下次重击·爆裂伤害提升120%。`,
  },
  {
    title: "颠覆无常",
    desc: `共鸣回路·冰绽的暴击伤害提升100%。
即使没有成功引爆，【冰棘】、【冰棱】、【冰川】也会在消失时直接爆炸。`,
  },
  {
    title: "曙色天光",
    desc: `引爆【冰棱】或【冰川】后，队伍中的角色攻击提升10%，持续20秒，可叠加2层。`,
  },
]);

const reducedMotion =
  window.matchMedia &&
  window.matchMedia("(prefers-reduced-motion: reduce)").matches;

const items = [
  {
    id: "1",
    title: "令尹近卫之名",
    content: `比起“散华”，他人对她更为常用的称呼是“那位令尹近卫”。

当人们见到她时，她总缄默地站在今州年轻的令尹今汐的身后，保持着不远不近的距离，宛如一柄利刃，在必要时以锋芒逼人，更多时候只是低调地隐藏在他人的余光中。

尽管大多反对今令尹的声音已经消弭，但偶尔仍有人试图挑战她的权威，碍于今州令尹的官职权威，她身边这个看起来不近人情、又不懂变通的近卫，正是最合适的施压对象。

然而这些人的如意算盘一次都没能打响，无论不怀好意的来客提出多么苛刻的诉求，散华都能分毫不差地完成到极致。

永远严谨的时间观念，对答如流的知识储备，冷静灵活的应急处理，缜密圆滑的谈判手段……让最吹毛求疵的人都无法从她身上挑出任何问题。

作为“令尹近卫”，除了要应付在阳光之下的刁难来客，散华更加重要的工作是“接待”那些阴影之下的不速之客。

令尹的地位有多高贵，今汐的命就有多值钱，为钱而来的客人，贪图的可不止一杯热茶。

但他们很快就会发现自己的失算，迎接他们的不是热茶，而是凛冽的刀锋，那名近卫有一双似乎能轻易洞察一切谎言的赤瞳，杀意让她瞬间化为可怖的修罗——

刀出，刃闪，对方被利落整齐割断的裤腿便簌簌滑到鞋边，露出在骤降的冷空气中不断打颤的小腿。

仅此一举，便足以处理掉大部分不速之客。

当然，在归刀入鞘之后，这一切便不复存在，阴影再次归于阴影，无法撼动今汐的任何一缕光辉。

但也许，凡事也总有意外……

“这位是令尹近卫？”

眼前与今汐一样有着漂亮且清晰的人形频率的共鸣者询问道。

——那位令尹近卫的世界，再次产生了涟漪。`,
  },
  {
    id: "2",
    title: "骤雪",
    content: `散华的故事始于雪。

一场突如其来爆发的海蚀异象吞噬了散华所在的城镇，大雪封山，残象异动，生灵涂炭，聚集所中的人类、草木、动物，一切生灵与无机物，短短数时便在异象暴雪中被击溃、碾碎，奇异地重组为巨大的残象，矗立在已化为无音区的雪山顶上，貌似一尊宝象庄严、又诡谲妖异的非天像。

全力以赴抵达现场的夜归军击败了残象，诡谲的巨型残象在尖啸中坍塌、消解。清扫战场时，有人惊奇地在战场残骸中发现一个浑身落雪、冰冻青紫的女婴，尚有一丝呼吸。

没有人能想象婴孩如何在这般炼狱中活下来，也没有人来得及去想这样的事，夜归的战士擦拭掉手上的血污，小心翼翼地将躺在一片狼藉之中的女婴抱起，朝后方高呼着“这里有个小婴儿！还有人活着，还有人活着！”

战士的喊声远远传到战线的后方，夜归们鼓舞起来，对生命的眷恋与感激之情随着呐喊传遍了战场，抚慰了战士们疲倦的心灵。

人们以为这是“神明恩泽的奇迹”——

数日后，被从地狱中夺回的婴孩初次睁开了那双摄人心魂的赤色异瞳。残象没有夺走她的生命，而是侵蚀了她的右目，残响的频率奇迹般重组成一只新生的眼睛，令天生无法视物的她，从此用这只眼睛便能够洞穿世间万物频率本质。

——殊不知，这也是“来自悲鸣的诅咒”。

诅咒将万物扭曲成姿态怪异可怖、嘈杂不堪的鬼怪，扰乱她的感官，碾碎她对世间一切的认知。没有谎言，却也没有光芒。

她只能陷在当初那个雪夜的无尽轮回当中，不断重复地从地狱中踏入世间，再坠回炼狱，直至成为修罗恶鬼。她从未从战场中被解救。

自此，她的世界成为炼狱绘图，此生都将于残象纠缠共生，无法解脱。`,
  },

  {
    id: "3",
    title: "余寒",
    content: `散华偶尔会梦见雪。

散华年幼时便沉默寡言，不擅与他人来往。她周身总不受控地裹挟着令人寒颤的冰冻寒气，行走在路上如同一把尖刀割开空气，将她与他人隔绝开，无辜过路的人也要提防自己被无故冻伤。

她警惕，危险，难以捉摸。他人大抵难以想象，这样的散华也会反复做一个关于雪的噩梦。

在那个梦境中，她似乎迷失了，行走在无尽漆黑中追寻一片雪原、或是一缕微光。转瞬她发觉自己躺在雪地上，数个扭曲的怪物围着她啃噬她，她孤零零躺在数百具支零破碎的尸首上，眼睁睁看着那些怪物挖出她的眼睛，直到她眼前陷入一片黑暗，只剩下怪物的影子簌簌动着，这时她便会惊醒。

随着年龄增长，她的共鸣能力日趋强大，蠢蠢欲动的恶鬼蛰伏在她的体内，残象渴望侵蚀频率的本能驱使着她——风雪气象愈加难以受她抑制，她连人与残象的频率也要区分不开了。

有时，她认为自己醒来却仍在梦中，低头再看，自己也是怪物的模样。

究竟是她无情，还是风雪无情，抑或她正是风雪？

面临必定与恶鬼同行的结局，散华踏上了自我流放的旅途。

如今散华仍然会梦见雪。

她不需要在黑夜中开灯也能视物，因此当从她梦中惊醒，察觉到房内有光源时，就知道那是今汐仍在忙着处理公文事务。

“抱歉，令尹……”她因失职而歉意。

灯下少女伏案疾书，察觉到动静后抬起头，“都让你先去休息了。没有你昨夜通宵帮我整理好，我办到明早也做不完一半。”

“令尹请去休息吧，将这些交给我就好。”
“又像昨晚那样让你独自做完这些繁琐的公文吗？”今汐思索片刻，又露出略带调皮的笑，“你陪我说话解解闷就好，我就快处理完了。”

“好的，令尹想要说什么？需要我为您念诵心经吗？”

“哎呀，你方才是做梦了吗？似乎听到你在嘟嘟嚷嚷说一些梦话。”

“应该是在梦中念心经吧。”

“……如何还是心经！实不相瞒，处理这些公文的确有些疲了……”

“……嗯。我梦到今汐大人了，今汐大人站在一片雪地中……很美。”

散华回应少女以微笑。如今，她的旅途已不再是独自一人。`,
  },

  {
    id: "4",
    title: "落华",
    content: `散华曾见过雪。

她在流放之旅中辗转多地，向人烟稀少的山林、平原出发，站在悬崖上俯瞰漆黑的无际大地。

她看见星痕状的裂缝盘横在这颗星球上，残象自其中源源不断地孵化，浩荡成军，如同曾侵蚀她的双眼一般侵蚀这片人类赖以生存的土地。

她企图在斩断残象中获得解脱，却夺不回任何平静。无序且疯狂的频率的嘈杂声仍然日夜在她耳畔尖啸，唯有生存的本能支撑着她、逼迫她继续前行。

她像是一只由细线牵引的风筝漂浮在荒野上，直至那根细线也要堪堪断掉，她任由风雪肆虐，漫无目的地游行，所及之处皆被暴雪卷席。她成为灾难本身。

她的恶名传开，陆续有追捕她的人，她不清楚是谁，也不知为何，一线理智令她意识到要将恶鬼关入囚笼中，可慌乱与本能又令她下意识地狼狈逃窜。

直到在一次包围中，她被赶至山中，身陷囹圄之间，她“看”见了雪——

金色的雪，绚烂如花瓣洒落漫天之中。

她不由自主地被吸引前去，忘却了处境与伤痛。随即她听见低沉的吟诵响起，那声音仿佛有种魔力，令人心神逐渐平静，待她回过神来，自己已站在那片金色的雪中，周身的风雪也逐渐消散了。

站在她面前的人自称住持，那时她才知道，原来眼前景象不过是菩提落花。

“一切有为法，如梦幻泡影。如露亦如电，应做如是观。你所视为苦，殊不知知苦求出离……缘者，助因成果。”

她似懂非懂，却没有再逃走。寺庙的主持收留了她……或者说，她被抓入了寺庙中，如同一只被捉起来的乡间野猫。无人料到最终驯服她的不是武力，而是一场无声落花与一句劝诫。

她被赐法号“散华”，此后便作为“散华”与庙中其余人一同修习练剑、念诵心经。

她思考，若缘者助因成果，那么她的因缘，究竟要将她引向何果？`,
  },

  {
    id: "5",
    title: "初霁",
    content: `散华再次站在血火交融的战场里，为了那些又一次被从她身边夺走的事物。

多年前，是她的眼睛，她的村落，当时她尚且没有反抗的能力；而如今她矗立战阵之中，风雪是她的诅咒、也是她的武器。

她双目被乌泱泱的残象刺痛，耳边唯闻频率失真的尖啸，在风雪肆虐中，眼前的景象逐渐与许多年不再出现的那场噩梦重合了。

她看见数十……乃至数百具尸首堆叠一起，似是一尊巨大可怖的修罗相，而与此前的梦不同的是，她定睛一看，认出其中面孔都来自她身边之人——

幻境中，她体内的恶鬼撕破了她这具如今空荡荡的躯壳，裹挟着暴雪狂乱而至。

不再是她操纵能力，而是能力反噬她、驱使着她毁灭。她那来自诅咒的力量，在悲鸣面前微不足道得像个笑话。

“……原来一切都不曾改变过。”

直至她不敌残象与异象，精疲力竭倒在地上，她喃喃自语，无力地叹息着。

散华不知自己身上有多少伤，只觉得仅有的温暖也在离她远去，她回到了年幼的梦中，又躺在那块雪地上，冰冷包裹着她，身体仿佛要融化到雪地中。

在世界即将回归全寂与漆黑之时，一片白色落入了她模糊的视野中。

“……雪……”

她伸出手去，想要触碰那一片冰冷，指尖却被裹入温暖之中。

——不，那不是雪花。

散华吃力地眨着眼，视线中逐渐浮现的是一道清晰的少女的身形，她从未见过这样的频率。

少女挡在她身前，似有雷光在她指尖流动。散华周身毫无章法四处肆掠的暴雪被引至少女身侧，混沌无序的频率在引导中逐渐趋于稳定，天边春雷乍动，气节流转，四季轮换，少女如同回应祈愿而降临的神子，眩目雷光顷刻间便将战场残象瓦解。

冰消雪融，散华耳中的尖啸逐渐平息，听到的不再是战场上的哭号与悲鸣，而是风吹，鸟鸣，大地复苏的声音，这一刻，她似乎终于踏出了多年前的那个雪夜，岁月时光再次在她生命中流动起来。

自那一瞬起，散华明了了她的因果。

缘者，助因成果。今汐让她见证毁灭中也会有新生，而今汐……也许正是能在这样的世道下护存庇佑新生，引领子民的人。

今汐所引向的那个未来，正是她所寻求的缘果；是她一直以来在漆黑中追寻的光芒。

——这一次，她执起刃，将环绕在她身边的恶鬼，化为了守护的力量。
`,
  },
];
const current = ref(0);

function select(index) {
  current.value = index;
  // 将焦点移回当前 tab（便于无障碍与键盘导航）
  const id = `tab-${items[index].id}`;
  const el = document.getElementById(id);
  if (el) el.focus();
}
const storyRef = ref<HTMLElement | null>(null);

function prev() {
  current.value = (current.value - 1 + items.length) % items.length;
  select(current.value);
  storyRef.value.scrollIntoView({ behavior: "smooth", block: "start" });
}

function next() {
  current.value = (current.value + 1) % items.length;
  select(current.value);
  storyRef.value.scrollIntoView({ behavior: "smooth", block: "start" });
}
interface Chibi {
  src: string;
  top: number;
  left: number;
}

const chibiList = ref<Chibi[]>([]);
onMounted(async () => {
  if (!reducedMotion) {
    const tl = gsap.timeline({ defaults: { ease: "power3.out" } });
    tl.from(".avatar-card", {
      duration: 0.8,
      y: 18,
      opacity: 0,
      scale: 0.99,
    }).from(
      ".right-col > .hero-panel .name",
      { duration: 0.9, x: 24, opacity: 0 },
      "-=0.45"
    );

    gsap.utils.toArray(".tl-item").forEach((it: any, idx: number) => {
      gsap.from(it.querySelector(".tl-card"), {
        scrollTrigger: {
          trigger: it,
          start: "top 85%",
          toggleActions: "play none none reverse",
        },
        opacity: 0,
        y: 22,
        duration: 0.7,
        delay: idx * 0.06,
      });
    });

    gsap.to(".avatar-card", {
      scale: 1.007,
      duration: 2.8,
      yoyo: true,
      repeat: -1,
      ease: "sine.inOut",
    });
  } else {
    document.documentElement.classList.add("reduced-motion");
  }

  // // 1. 基础配置信息
  // const total = 6;
  // let pickCount = 3; // 每次抽取 3 张
  // const vw = window.innerWidth;
  // const vh = window.innerHeight;
  // const isMobile = window.innerWidth <= 768;
  // // 如果已知单张小人图片的宽高，可避免超出边界；
  // // 假设小人图片宽 100px、高 100px，按需替换：
  // const imgWidth = 100;
  // const imgHeight = 100;

  // // 2. Fisher–Yates 洗牌函数
  // function shuffle(array) {
  //   for (let i = array.length - 1; i > 0; i--) {
  //     const j = Math.floor(Math.random() * (i + 1));
  //     [array[i], array[j]] = [array[j], array[i]];
  //   }
  //   return array;
  // }

  // // 3. 随机选出 3 个编号
  // if (isMobile) {
  //   pickCount = 1;
  // }
  // const nums = shuffle(Array.from({ length: total }, (_, k) => k + 1));
  // const picks = nums.slice(0, pickCount);

  // // 4. 生成随机位置并填充 chibiList
  // chibiList.value = []; // 先清空
  // picks.forEach((i) => {
  //   chibiList.value.push({
  //     src: `/QImages/1 (${i}).png`,
  //     left: Math.random() * (vw - imgWidth), // 保证不超出左右边界
  //     top: Math.random() * (vh - imgHeight), // 保证不超出上下边界
  //   });
  // });

  // // 2. 等 img 渲染到 DOM
  // await nextTick();

  // // 3. 给每个小人绑定 GSAP 动画
  // const imgs = document.querySelectorAll<HTMLImageElement>(".chibi-img");
  // imgs.forEach((img, index) => {
  //   const padding = 200; // 边缘预留空间
  //   // ✅ 初始出场动画（闪现）
  //   gsap.fromTo(
  //     img,
  //     { opacity: 0, scale: 0.5 },
  //     {
  //       opacity: 1,
  //       scale: 1,
  //       duration: 0.8,
  //       ease: "back.out(2)",
  //       delay: 0.2 * index,
  //     }
  //   );

  //   // ✅ 鼠标靠近闪避
  //   img.addEventListener("mouseenter", () => {
  //     gsap.killTweensOf(img);

  //     gsap.to(img, {
  //       x: "+=" + ((Math.random() - 0.5) * 400).toFixed(0),
  //       y: "+=" + ((Math.random() - 0.5) * 400).toFixed(0),
  //       duration: 1.2,
  //       ease: "back.out(2)",
  //       onComplete: () => {
  //         // 闪避完成后，再重新启用动画
  //         animate(img);
  //       },
  //     });
  //   });

  //   const animate = (img: HTMLImageElement) => {
  //     let { x, y } = img.getBoundingClientRect();
  //     let deltaX = (Math.random() - 0.5) * 200;
  //     let deltaY = (Math.random() - 0.5) * 200;

  //     // 预测一下偏移后的位置
  //     let nextX = x + deltaX;
  //     let nextY = y + deltaY;

  //     // 校正：防漂出左、右、上、下边界
  //     if (nextX < padding) deltaX = padding - x;
  //     if (nextX + img.width > window.innerWidth - padding)
  //       deltaX = window.innerWidth - padding - (x + img.width);
  //     if (nextY < padding) deltaY = padding - y;
  //     if (nextY + img.height > window.innerHeight - padding)
  //       deltaY = window.innerHeight - padding - (y + img.height);

  //     gsap.to(img, {
  //       x: `+=${deltaX.toFixed(0)}`,
  //       y: `+=${deltaY.toFixed(0)}`,
  //       rotation: `+=${((Math.random() - 0.5) * 60).toFixed(0)}`,
  //       duration: 2 + Math.random() * 2,
  //       ease: "power1.inOut",
  //       onComplete: () => animate(img),
  //     });
  //   };
  //   animate(img);
  // });
});
</script>

<style scoped lang="scss">
@use "sass:color";

/* 散华风格 — 冷凝 / 刃霜 / 猩红点缀 */
$bg-deep: #051025; // 极夜深底（页面主底）
$bg-mid: #0b2432; // 中层渐变
$ice-core: #eaf7ff; // 冰晶高光
$accent-ice: #6fd0ff; // 冷蓝反光
$blade-gray: #162029; // 刀锋 / 金属暗色
$glass-layer: rgba(8, 12, 18, 0.64); // 半透明玻璃层
$crystal-glow: rgba(120, 208, 255, 0.08); // 晶体泛光
$scarlet: #c84b4b; // 猩红点缀（极少量）
$text-main: #dbeefb; // 深底上的主文字
$text-muted: rgba(219, 234, 247, 0.72);
$card-bg: rgba(6, 10, 16, 0.56); // 卡片背景（半透暗）
$card-border: rgba(111, 208, 255, 0.04); // 卡片边框（冰蓝）
$soft-shadow: rgba(3, 8, 14, 0.6);
$inner-glow: rgba(120, 208, 255, 0.03);
$grain: rgba(255, 255, 255, 0.02);

/* 固定浮动小 chibi 层（保留但风格冰化） */
.floating-chibis {
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 1;

  .chibi-img {
    position: absolute;
    width: 80px;
    user-select: none;
    transform-origin: center center;
    pointer-events: auto;
    z-index: 10;
    filter: drop-shadow(0 10px 22px rgba(3, 8, 14, 0.45)) saturate(0.9)
      contrast(0.95);
    transition: transform 360ms cubic-bezier(0.2, 0.9, 0.2, 1);
  }
}

/* 页面主体 */
.megumi-page {
  position: relative;
  min-height: 100vh;
  background: linear-gradient(180deg, $bg-deep 0%, $bg-mid 72%);
  font-family: "Noto Sans SC", system-ui, -apple-system, "Segoe UI", Roboto,
    Arial;
  color: $text-main;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  padding: clamp(20px, 4vw, 80px);
  box-sizing: border-box;
  padding-top: 60px;

  &.reduced-motion * {
    transition: none !important;
    animation: none !important;
  }

  .container {
    position: relative;
    z-index: 10;
    max-width: 1100px;
    margin: 0 auto;
    padding: clamp(12px, 2vw, 40px);
  }
}

/* ========== HERO 区域（暗冷 + 冰晶点光） ========== */
.hero {
  position: relative;
  display: grid;
  grid-template-columns: 280px 1fr;
  gap: 28px;
  align-items: start;
  padding: 12px;
  margin-bottom: 12px;

  /* 背景斜纹 — 模拟霜层 / 冰晶噪点 */
  &::before {
    content: "";
    position: absolute;
    inset: 0;
    z-index: 0;
    pointer-events: none;
    background-image: repeating-linear-gradient(
      135deg,
      $grain 0px,
      transparent 2px,
      transparent 18px
    );
    opacity: 0.06;
    mix-blend-mode: overlay;
    transform: translateY(-4%);
    animation: staff-drift 18s linear infinite;
  }

  .left-col {
    display: flex;
    flex-direction: column;
    align-items: center;
    z-index: 6;
  }

  .avatar-card {
    background: linear-gradient(
      180deg,
      rgba(255, 255, 255, 0.02),
      rgba(120, 208, 255, 0.02)
    );
    border-radius: 16px;
    padding: 12px;
    display: grid;
    justify-items: center;
    gap: 8px;
    border: 1px solid $card-border;
    box-shadow: 0 14px 44px $soft-shadow, inset 0 1px 0 $inner-glow;
    cursor: pointer;
    transition: transform 260ms cubic-bezier(0.2, 0.9, 0.2, 1),
      box-shadow 260ms ease;
    width: 100%;

    &:hover {
      transform: translateY(-6px);
      box-shadow: 0 28px 64px rgba(3, 8, 14, 0.7);
      border-color: rgba(111, 208, 255, 0.08);
    }
  }

  .avatar-wrap {
    width: 100%;
    display: block;
  }

  .avatar {
    width: 100%;
    height: auto;
    border-radius: 12px;
    display: block;
    transition: transform 420ms cubic-bezier(0.2, 0.9, 0.2, 1),
      filter 420ms ease;
    background-color: rgba(0, 0, 0, 0.06);
    filter: saturate(0.9) contrast(0.95) brightness(0.92);
    box-shadow: 0 12px 30px rgba(3, 8, 14, 0.45) inset;
  }

  .avatar-card:hover .avatar {
    transform: translateY(-6px) scale(1.02);
    filter: saturate(0.85) contrast(0.9) brightness(0.9);
  }

  .avatar-meta {
    display: flex;
    gap: 8px;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;
  }

  .avatar-badge {
    font-size: 0.9rem;
    color: $text-main;
    background: rgba(0, 0, 0, 0.18);
    padding: 6px 10px;
    border-radius: 999px;
    border: 1px solid rgba($accent-ice, 0.04);
    font-weight: 600;
  }

  .right-col {
    display: flex;
    flex-direction: column;
    gap: 12px;
    z-index: 6;
  }

  .hero-panel {
    position: relative;
    z-index: 6;
    padding: 20px;
    border-radius: 14px;
    background: $card-bg;
    backdrop-filter: blur(8px) saturate(0.9);
    border: 1px solid $card-border;
    box-shadow: 0 12px 50px rgba(0, 0, 0, 0.64), inset 0 1px 0 $inner-glow;
  }

  .name {
    font-size: clamp(1.8rem, 3vw, 2.8rem);
    margin: 0;
    font-weight: 900;
    letter-spacing: 1px;
    line-height: 1;
    display: inline-block;
    /* 标题：冷银 → 猩红微光（控制猩红占比极低） */
    background: linear-gradient(
      90deg,
      $ice-core 0%,
      $accent-ice 56%,
      rgba($scarlet, 0.6) 92%
    );
    -webkit-background-clip: text;
    background-clip: text;
    -webkit-text-fill-color: transparent;
    color: transparent;
    -webkit-text-stroke: 0.2px rgba(0, 0, 0, 0.22);
    position: relative;
    text-shadow: 0 8px 22px rgba(2, 6, 10, 0.44);
  }

  .jp {
    font-size: 0.95rem;
    color: $accent-ice;
    margin-left: 8px;
    font-weight: 400;
  }

  .subtitle {
    margin: 6px 0 0;
    font-size: 1.05rem;
    color: $text-muted;
    font-weight: 600;
    line-height: 1.4;
  }

  .lead {
    margin: 10px 0 0;
    color: $text-main;
    opacity: 0.96;
    line-height: 1.7;
    max-width: 72ch;
  }
}

/* ========== 通用卡片（更暗、更锐利的边与冰蓝提示） ========== */
.card {
  margin-top: 24px;
  background: rgba(8, 12, 18, 0.56);
  border-radius: 14px;
  padding: 24px;
  box-shadow: 0 12px 48px rgba(0, 0, 0, 0.68);
  backdrop-filter: blur(6px);
  color: $text-main;
  border: 1px solid $card-border;
  transition: transform 260ms cubic-bezier(0.2, 0.9, 0.2, 1),
    box-shadow 260ms ease;

  &:hover {
    transform: translateY(-8px) scale(1.01);
    box-shadow: 0 30px 90px rgba(0, 0, 0, 0.78);
  }

  h2 {
    margin: 0 0 12px 0;
    font-size: 1.3rem;
    color: $accent-ice;
  }

  .card-head {
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 12px;
  }
}

/* overview 双栏布局（保持） */
.overview .two-col {
  display: flex;
  gap: 24px;
  flex-wrap: wrap;
  margin-top: 12px;
}

.overview .text-block {
  flex: 1 1 40%;
  min-width: 240px;
  line-height: 1.6;
  color: rgba($text-main, 0.96);
}

/* special-section：加入冰晶漂浮和冷蓝边框 */
.special-section {
  position: relative;
  overflow: hidden;

  .content {
    display: flex;
    gap: 24px;
    align-items: center;
    margin-top: 16px;
  }

  .img-area {
    flex: 0 0 200px;

    img {
      width: 100%;
      height: auto;
      border-radius: 12px;
      border: 1px solid rgba($accent-ice, 0.03);
      box-shadow: 0 10px 26px rgba(3, 8, 14, 0.48);
    }
  }

  .text-block {
    flex: 1;
    line-height: 1.6;
    color: rgba($text-main, 0.95);
  }
}

/* ll-stories — 暗底 + 微猩红分割（微弱） */
.ll-stories {
  .card {
    width: min(1200px, 100%);
    border-radius: 12px;
    color: $text-main;
    overflow: hidden;
    display: flex;
    flex-direction: column;
  }

  .card-head {
    padding: 14px 16px;
    border-bottom: 1px solid rgba($scarlet, 0.02);
    display: flex;
    flex-direction: column;
    gap: 12px;

    .card-title {
      font-size: 24px;
      font-weight: 700;
      color: $accent-ice;
      letter-spacing: 0.6px;
    }
  }

  .card-body {
    padding: 18px 16px;
    min-height: 160px;
    position: relative;
    background: linear-gradient(180deg, rgba(0, 0, 0, 0), rgba(8, 8, 10, 0.02));
  }

  .story {
    color: rgba($text-main, 0.96);

    .story-head {
      font-size: 18px;
      color: rgba($scarlet, 0.9);
      font-weight: 700;
      margin-bottom: 8px;
    }

    .story-content {
      line-height: 1.7;
      color: rgba($text-main, 0.92);
      white-space: pre-wrap;
    }
  }

  .card-foot {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 8px;
    padding: 10px 14px;
    border-top: 1px solid rgba($accent-ice, 0.02);
    background: linear-gradient(
      180deg,
      rgba(0, 0, 0, 0.02),
      rgba(0, 0, 0, 0.04)
    );

    .nav-btn {
      background: rgba($text-main, 0.6);
      border: 1px solid rgba($accent-ice, 0.04);
      color: rgba($ice-core, 0.98);
      padding: 6px 10px;
      border-radius: 6px;
      cursor: pointer;
      transition: background 180ms ease;
      &:hover {
        background: rgba(0, 0, 0, 0.9);
      }
    }

    .pager {
      color: rgba($text-main, 0.72);
      font-size: 13px;
      font-weight: 600;
    }
  }

  /* 切换动画：更凝重的缓出入 */
  .fade-slide-enter-active,
  .fade-slide-leave-active {
    transition: all 360ms cubic-bezier(0.2, 0.9, 0.2, 1);
  }

  .fade-slide-enter-from {
    opacity: 0;
    transform: translateY(8px) scale(0.996);
  }

  .fade-slide-enter-to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }

  .fade-slide-leave-from {
    opacity: 1;
    transform: translateY(0);
  }

  .fade-slide-leave-to {
    opacity: 0;
    transform: translateY(-8px);
  }
}

/* timeline（冷蓝竖线 + 微光） */
.timeline {
  position: relative;

  .tl-wrap {
    position: relative;
    padding: 24px 12px 12px 96px;
  }

  .vl {
    position: absolute;
    left: 80px;
    top: 32px;
    bottom: 20px;
    width: 4px;
    border-radius: 6px;
    background: linear-gradient(
      180deg,
      rgba($accent-ice, 0.12),
      rgba($ice-core, 0.06)
    );
    box-shadow: 0 6px 18px rgba($accent-ice, 0.06),
      inset 0 0 10px rgba($accent-ice, 0.02);
    opacity: 0.95;
  }

  .tl-list {
    list-style: none;
    margin: 0;
    padding: 0;
  }

  .tl-item {
    position: relative;
    display: flex;
    gap: 18px;
    align-items: flex-start;
    margin: 20px 0;
    transition: transform 260ms cubic-bezier(0.2, 0.9, 0.2, 1),
      box-shadow 260ms ease;
  }

  .tl-card {
    border-radius: 12px;
    padding: 16px 20px;
    width: calc(100% - 130px);
    border-left: 4px solid transparent;
    transition: transform 260ms cubic-bezier(0.2, 0.9, 0.2, 1),
      box-shadow 260ms ease, border-color 260ms ease;
    position: relative;
    z-index: 2;
  }

  .tl-item:hover .tl-card,
  .tl-item:focus-within .tl-card {
    transform: translateY(-8px);
    box-shadow: 0 24px 72px rgba(0, 0, 0, 0.68);
    border-left-color: rgba($accent-ice, 0.18);
  }

  .tl-title {
    margin: 0 0 6px 0;
    font-size: 1.1rem;
    color: $accent-ice;
    display: flex;
    align-items: baseline;
    gap: 8px;
  }

  .tl-desc {
    margin: 0;
    color: rgba($text-main, 0.92);
    white-space: pre-wrap;
  }
}

/* 浮动音符（保留，但色调改为冷蓝） */
.floating-note {
  position: absolute;
  font-size: 14px;
  color: $accent-ice;
  opacity: 0.95;
  transform-origin: center;
  animation: note-float 4.6s ease-in-out infinite;
  filter: drop-shadow(0 6px 18px rgba($accent-ice, 0.06));
}

/* 动画关键帧（冰层漂移 / 冰片漂浮 / 光点） */
@keyframes staff-drift {
  0% {
    transform: translateY(-4%);
    opacity: 0.9;
  }
  50% {
    transform: translateY(6%);
    opacity: 0.95;
  }
  100% {
    transform: translateY(-4%);
    opacity: 0.9;
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

/* 响应式：小屏适配（保留原逻辑，仅小幅调整文字色调与阴影） */
@media (max-width: 980px) {
  .hero {
    grid-template-columns: 1fr;
    gap: 18px;
    padding: 8px;
  }

  .avatar-card {
    width: 140px;
    padding: 8px;
  }

  .hero-panel {
    padding: 14px;
  }

  .name {
    font-size: 1.8rem;
  }

  .subtitle {
    font-size: 1rem;
  }

  .lead {
    font-size: 0.95rem;
  }

  .card {
    padding: 18px;
    border-radius: 12px;
  }

  .overview .two-col {
    flex-direction: column;
  }

  .special-section .content {
    flex-direction: column;
  }

  .ll-stories {
    .card {
      border-radius: 10px;
    }

    .card-head {
      padding: 12px 12px;
    }

    .card-body {
      padding: 14px 12px;
      min-height: 180px;
    }

    .card-foot {
      padding: 8px 12px;
    }
  }

  .timeline {
    overflow: visible;
  }

  .timeline .tl-wrap {
    padding-left: 14px !important;
    padding-right: 14px;
    min-height: 0;
  }

  .timeline .vl {
    display: none !important;
  }

  .timeline .tl-list {
    overflow: visible;
  }

  .timeline .tl-item {
    display: block !important;
    margin: 14px 0 !important;
    width: 100%;
  }

  .timeline .tl-card {
    width: 100% !important;
    padding: 14px !important;
    border-left: 0 !important;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.45);
    transform: none !important;
  }
}
</style>
