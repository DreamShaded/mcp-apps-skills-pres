---
layout: interjection
variant: 2
transition: fade
contentPos: center
contentAlign: center
---

<TextBig>Skills Over MCP</TextBig>

<!--
От ui:// — интерфейса для человека — переходим ко второму ресурсу, skill://: это инструкция для агента. Рабочая группа Skills Over MCP формулирует это так: скиллы — про управление контекстом, а MCP — протокол контекста. Агент уже ходит на сервер за тулами; за инструкцией он может ходить туда же.
-->

---
layout: simple-slide
variant: 11
---

# Ограничения

<div class="limits">
  <div class="lim" v-click><span class="n">1</span><div>инструкции сервера грузятся только на инициализации<br><span class="sub">новый или обновлённый скилл — переподключение</span></div></div>
  <div class="lim" v-click><span class="n">2</span><div>сложные процедуры не влезают в размер инструкции<br><span class="sub">сотни строк markdown со ссылками на файлы</span></div></div>
  <div class="lim" v-click><span class="n">3</span><div>нет discovery<br><span class="sub">пользователь не знает, что к серверу есть скилл</span></div></div>
  <div class="lim" v-click><span class="n">4</span><div>оркестрация через несколько серверов<br><span class="sub">description принадлежит одному тулу на одном сервере</span></div></div>
</div>

<style scoped>
.limits { margin-top: 1.4rem; display: grid; grid-template-columns: 1fr 1fr; gap: 1.2rem 2rem; }
.lim { display: flex; gap: 1rem; align-items: flex-start; font-size: 1.4rem; line-height: 1.35; }
.lim .n { flex: none; width: 2.2rem; height: 2.2rem; border-radius: 50%; display: grid; place-items: center; font-weight: 800; background: var(--dark-blue); color: #fff; }
.lim .sub { color: var(--white-sub); font-size: 1.2rem; }
</style>

<!--
почему скиллы неудобно раздавать через обычные механизмы MCP. Четыре места, куда скилл пытаются засунуть, и почему каждое не тянет:
Ограничение 1 — server instructions грузятся только при старте. Инструкции сервера модель читает один раз, когда подключается. Добавил новый скилл или поправил старый — пользователю надо переподключиться, иначе модель о нём не узнает. Живого обновления нет.
Ограничение 2 — скилл не влезает в инструкцию по размеру. Настоящий скилл это сотни строк markdown со ссылками на файлы. В поле инструкции столько не запихнёшь — оно не под это.
Ограничение 3 — нет discovery (обнаружения). Пользователь не видит, что у сервера вообще есть скилл. Никакого списка «вот что я умею по шагам» — про скилл надо знать заранее.
Ограничение 4 — не работает оркестрация через несколько серверов. description (описание) привязан к одному тулу на одном сервере. А скилл, который дирижирует тулами с разных серверов, некуда положить: нет общего места, которое видит их все.
-->

---
layout: two-cols
variant: 11
leftWidth: 50%
align: center
---

::header::

# Анатомия скилла

::default::

<div v-click="1">

```
purchase-flow/
├── SKILL.md          ← фронтматтер + процедура
├── references/
│   └── delivery-rules.md
├── examples/
│   └── out-of-stock.md
└── scripts/          ← в MCP не исполняются
```

</div>

::right::

<div class="prog">
  <div class="p" v-click="2"><b>фронтматтер</b><br>name · description</div>
  <div class="p" v-click="3">в контексте постоянно — только <b>две строки</b></div>
  <div class="p" v-click="4">тело приезжает при попадании в сценарий</div>
</div>

<style scoped>
:deep(.slidev-code) { font-size: 0.95rem; }
.prog { display: flex; flex-direction: column; gap: 1.2rem; }
.p { font-size: 1.45rem; line-height: 1.4; padding-left: 1.2rem; border-left: 4px solid var(--dark-blue); }
</style>

<!--
[click] Скилл — это директория. и вся хитрость в том, что в контекст модели постоянно висят только две строки, а тело подгружается, когда реально понадобилось.
Слева — дерево папки purchase-flow/:
SKILL.md — сердце скилла. Делится на две части: фронтматтер (шапка в начале файла: name и description) и тело (сама процедура — что делать по шагам).
references/ и examples/ — вспомогательные файлы: правила доставки, пример «товара нет». Модель лезет в них по ссылке, когда нужно, а не держит постоянно.
scripts/ — тут важная пометка: в MCP не исполняются. В обычных Skills у Claude в этой папке лежат запускаемые скрипты, но через MCP их не выполняют — только читают как текст. Это честное ограничение, хорошо что оно на слайде.
Справа — три буллета про экономию контекста, и это главная мысль:
фронтматтер: name · description — то, чем скилл представляется.
в контексте постоянно — только две строки — модель держит в голове всё время лишь name и description из шапки. Дёшево: сто скиллов это двести строк, а не сто простыней.
тело (140 строк) приезжает при попадании в сценарий — полную инструкцию модель подтягивает, только когда задача совпала с описанием скилла. Это называется progressive disclosure (постепенное раскрытие): сначала видна вывеска, содержимое открывается по требованию.
-->

---

# Как хост находит и грузит

<div class="flow">
  <div class="node" v-click="1"><code>resources/read skill://index.json</code><span>каталог: name · description · url</span></div>
  <div class="node" v-click="2">хост решает, как показать модели<span>системный промпт · тул чтения · локальная ФС</span></div>
  <div class="fork">
    <div class="branch no" v-click="3"><b>запрос мимо</b><span>тело не грузится · контекст чист</span></div>
    <div class="branch yes" v-click="4"><b>запрос в сценарии</b><span>resources/read SKILL.md → модель идёт по процедуре</span></div>
  </div>
</div>

<style scoped>
.flow { margin-top: 1.2rem; display: flex; flex-direction: column; gap: 0.9rem; }
.node { padding: 0.9rem 1.2rem; background: rgba(255,255,255,0.04); border: 1px solid rgba(255,255,255,0.12); border-radius: 10px; font-size: 1.4rem; font-weight: 600; }
.node code { color: var(--color-cyan); background: transparent; font-size: 1.3rem !important; }
.node span { display: block; margin-top: 0.3rem; font-weight: 400; font-size: 1.2rem; color: var(--white-sub); }
.fork { display: grid; grid-template-columns: 1fr 1fr; gap: 1.4rem; }
.branch { padding: 0.9rem 1.2rem; border-radius: 10px; font-size: 1.35rem; }
.branch b { font-size: 1.45rem; }
.branch span { display: block; margin-top: 0.3rem; font-size: 1.2rem; color: var(--white-sub); }
.branch.no { border: 1px solid rgba(255,255,255,0.14); }
.branch.yes { border: 1px solid var(--color-green); background: rgba(86,255,113,0.06); }
.cav { margin-top: 0.4rem; font-size: 1.35rem; color: var(--white-sub); }
</style>

<!--
жизненный цикл скилла: как хост сначала узнаёт список скиллов, потом решает, как показать их модели, и грузит тело только когда запрос реально попал в тему. Это прямое продолжение прошлого слайда: там было «две строки висят, тело приезжает по надобности», тут — по какому механизму оно приезжает.
Читается сверху вниз, по шагам:
Верхний блок — discovery (обнаружение). Хост зовёт resources/read skill://index.json и получает каталог всех скиллов: у каждого name, description, url. Это та самая вывеска из прошлого слайда, только теперь видно, откуда она берётся — из индекс-файла. Здесь закрывается ограничение №3 «нет discovery»: список есть.
Второй блок — хост решает, как показать модели. Каталог у хоста на руках, дальше его дело, куда положить эти вывески: в системный промпт, отдать через тул чтения или подсунуть с локальной файловой системы. Разные хосты — по-разному, спека не диктует. Смысл: модель узнаёт про скиллы одним из этих способов.
Нижние два блока — развилка, грузить тело или нет:
запрос мимо — задача под скилл не подошла. Тело не читается, контекст остаётся чистым. Это и есть экономия: пока скилл не нужен, за него не платишь токенами.
запрос в сценарии (зелёный, потому что это целевой путь) — задача совпала с описанием. Хост зовёт resources/read SKILL.md, подтягивает тело, и модель идёт по процедуре — по тем самым пошаговым инструкциям.
-->


---

# Код

```markdown {1-4|6-8|11-12}{lines:true}
---
name: purchase-flow
description: Подбор и оформление с проверкой сроков.
---
## Порядок
3. check_stock с регионом. Обязательно до шага 4.
4. estimate_delivery. Без шага 3 — срок склада.
5. Не успеваем → list_pickup_points либо альтернатива.

## Чего не делать
- Не называть срок без check_stock.
- Не выдавать рейтинг за критерий подбора.
```


<style scoped>
:deep(.slidev-code) { font-size: 1rem; }
.note { margin-top: 1.2rem; font-size: 1.35rem; color: var(--white-sub); }
</style>

<!--
Вот скилл вживую. Сверху между чёрточками — вывеска: имя и описание, только это модель видит всегда. Ниже — сама инструкция обычным текстом: сначала проверь наличие с регионом, потом считай срок — иначе получишь срок склада, а не доставки.Строки 5–8 — блок «Порядок», сама процедура. правило связывает несколько тулов в цепочку. check_stock (проверка наличия) с регионом обязательна до estimate_delivery (расчёт срока), иначе модель назовёт срок склада вместо реального. Не успеваем по сроку → зовём list_pickup_points или предлагаем альтернативу. Одному тулу в description такое не впишешь — поэтому и нужен скилл. Не успеваем — предложи пункты выдачи. И отдельно список „чего не делать“. Заметьте: инструкция не просто командует, а объясняет, почему так — поэтому модель ей следует, а не спорит».
-->

---

# index.json

```json {lines:false}
{
  "skills": [{
    "name": "purchase-flow",
    "description": "Подбор и оформление заказа",
    "url": "skill://shop/purchase-flow/SKILL.md",
    "type": "skill-md"
  }]
}
```


<style scoped>
:deep(.slidev-code) { font-size: 1rem; }
.note { margin-top: 1.8rem; font-size: 1.5rem; font-weight: 700; color: var(--color-green); }
.note code { color: var(--color-green); background: transparent; }
</style>

<!--
index.json, который хост читает первым, чтобы узнать, какие скиллы вообще есть.
Что внутри: массив skills, в нём по одной записи на скилл. У purchase-flow четыре поля:
name и description — та самая вывеска в две строки. Именно их хост показывает модели, и по description («Подбор и оформление заказа») модель решает, тянуть ли тело.
url: skill://shop/purchase-flow/SKILL.md — адрес, где лежит полное тело. Пока запрос не попал в сценарий, по этому адресу никто не ходит; совпал — хост читает SKILL.md отсюда (нижний зелёный блок того слайда).
type: skill-md — тип записи, говорит хосту «это скилл в формате markdown», а не что-то другое. Нужен, чтобы каталог мог хранить разные виды записей и хост понимал, как их обрабатывать.
-->

---

# Фиксируем возможности

<div class="rules">
  <div class="rule" v-click>скиллы поверх MCP — только инструкция<span>скилл, исполняющий код - пока недоступен</span></div>
  <div class="rule" v-click>многофайловые скиллы едут архивом (zip, tar)<span>а не пофайлово</span></div>
  <div class="rule" v-click>хост не исполняет ничего локально без явного согласия<span>ни хуков, ни pre/post-скриптов, ни шелл-команд</span></div>
</div>

<style scoped>
.rules { margin-top: 1.6rem; display: flex; flex-direction: column; gap: 1.2rem; }
.rule { font-size: 1.6rem; font-weight: 600; padding: 0.9rem 1.3rem; border-left: 4px solid var(--dark-blue); background: rgba(255,255,255,0.03); }
.rule span { display: block; margin-top: 0.4rem; font-size: 1.3rem; font-weight: 400; color: var(--white-sub); }
</style>

<!--
[click] (привязка к транспорту): MCP просто отдаёт скилл как ресурс, а сам формат скилла принадлежит отдельной Agent Skills spec. Протоколу не нужно понимать, что такое скилл, — для него это текстовый ресурс. Отсюда и «только инструктор»: скилл учит модель, что делать, но исполнение остаётся за тулами. Скилл, который исполняет код, за скобки вынесен намеренно.
[click]  В индексе действительно два типа записи: type: skill-md — когда скилл это один SKILL.md, отдаётся файлом напрямую; type: archive — когда есть references/, examples/, scripts/, всё это едет одним архивом, агент качает и распаковывает. Но добирается-то хост до артефакта всё равно через resources/read по url из индекса — и до одиночного SKILL.md, и до архива. 
Третий пункт — «хост не исполняет ничего локально без явного согласия: ни хуков, ни pre/post-скриптов, ни шелл-команд». Верно и это прямое следствие первого пункта. Папка scripts/ в скилле существует (в Agent Skills она есть), но привязка через MCP её не исполняет — читает как текст. Никаких автоматических хуков и shell-команд при загрузке скилла; если что-то и запускается, то через обычные тулы с обычным подтверждением хоста. 
-->

---
layout: interjection
variant: 4
transition: fade
class: demo-fullbleed
---

<script setup>
const base = import.meta.env.BASE_URL
</script>

<video
  :src="base + 'videos/demo-4.mp4'"
  class="demo-video"
  autoplay
  muted
  loop
  playsinline
  controls
/>

<style scoped>
/* Видео на весь слайд: абсолют перекрывает паддинги темы и фон-подложку */
.demo-video {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: contain;
  background: #000;
}
</style>

<!--

-->
