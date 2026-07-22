---
layout: interjection
variant: 4
transition: fade
---

<TextBig>Демо</TextBig>

<!--
а пока мне бы хотелось поговорить о таких забавных вещах, которые происходят в вебе. 
Примерно 30 лет развития технологий и дизайна люди старательно делали интерфейсы более доступными, красивыми, понятными, от терминальных матричных тёмно зелёных буковок мы пришли к крупным понятным кнопкам, инпутам, дропдаунам, дейтпикерам, и вот два ка два шестой год - вбиваем текстом ну или в лучшем случае голосом в чат с моделькой запрос 
"подбери мне наушники такой-то цены, чтоб доставка туда то, магазин такой-то" и смотрим на текст. Да, есть конечно голосовой мод и так далее, но чёт на любителя эта вся история, как мне кажется. Чуть лучше показывает перплексити, прикрепляя картинки, но в целом это не те карточки товаров. Да и вообще видите, модель попыталась посмотреть страницу, а Мегамаркет ей не отдал - там только то, что удалось по метаинформации или ещё как-то выцепить, дай боже не додумано непонятно из чего. 
Сегодня я расскажу, как мы решаем эту проблему в Сбере.
-->

---

# Контракт тула

<div class="grid2">

<div class="tools">
<pre>search_products
get_product
list_variants
…</pre>
<div class="sub">20+ Tools</div>
</div>

```json {all|3|4-10|11}{lines:true}
{
  "name": "search_products",
  "description": "Поиск по каталогу: категория, цена, рейтинг.",
  "inputSchema": {
    "properties": {
      "query":    { "type": "string" },
      "category": { "enum": ["headphones"] }
    },
    "required": ["query"]
  },
  "annotations": { "readOnlyHint": true }
}
```

</div>

<style scoped>
.grid2 { display: grid; grid-template-columns: 0.9fr 1.6fr; gap: 2rem; align-items: start; margin-top: 1.4rem; }
.tools pre { font-size: 1.5rem; line-height: 1.6; color: var(--color-cyan); }
.tools .sub { margin-top: 0.8rem; font-size: 1.2rem; }
.grid2 :deep(.slidev-code) { font-size: 1.05rem; }
</style>

<!--
Естественно, чтобы модель могла видеть и вызывать поиск, детальную информацию, чекаут и корзину, мы можем подключиться к MCP. У меня получилось разложить на примерно 20 тулов, каждый из которых оформлен по всем канонам: подробный дескрипшн, в оригинале на английском языке, описан стандарт общения, аннотация, которая подскажет модели о том, что инструмент только читает, не меняет состояний. Я уже подключил эти тулы к Cloud Desktop - давайте посмотрим
-->

---

# С MCP

<style scoped>
.log { margin-top: 1.6rem; display: flex; flex-direction: column; gap: 0.8rem; }
.line { font-size: 1.5rem; padding: 0.7rem 1.1rem; background: rgba(255,255,255,0.04); border-left: 3px solid rgba(255,255,255,0.2); }
.line .who { display: inline-block; min-width: 5.5rem; color: var(--white-sub); }
.line .call { color: var(--color-cyan); font-weight: 700; }
.line.bad { border-left-color: #ff5a5a; color: #ff9a9a; }
.line.bad b { color: #ff5a5a; }
.verdict { margin-top: 1.8rem; font-size: 2rem; font-weight: 800; }
</style>

<!--
Выполняем Запрос: наушники для созвонов, до 5 тысяч, к пятнице.
Агент зовёт search_products, берёт первую модель по рейтингу и обещает доставку к пятнице.
вызвался тул, отработал корректно, сейчас показывать не буду, но на большинстве этих товаров будет доставка не завтра, и хоть внешне всё супер, по факту мы получим недостаточный результат. Хочется дать чего-то лучше, да? 
Давайте разбираться
-->

---
layout: two-cols
variant: 11
leftWidth: 42%
align: top
---

::header::

# Почему так

::default::

<div class="okcard" v-click="1">
  <div class="uri">estimate_delivery</div>
</div>

::right::

<div class="qs">
  <div class="q" v-click="2">В каком порядке вызвать инструменты?</div>
  <div class="q" v-click="3">Рейтинг — это критерий выбора?</div>
  <div class="verdict" v-click="4">Схема отвечает на вопрос «что делает этот тул».</div>
</div>

<style scoped>
.okcard { margin-top: 1rem; padding: 1.4rem 1.6rem; border: 2px solid var(--color-green); border-radius: 12px; background: rgba(86,255,113,0.06); }
.okcard .uri { font-size: 1.9rem; font-weight: 700; color: var(--color-cyan); }
.okcard .ok { margin-top: 0.6rem; font-size: 1.3rem; color: var(--color-green); }
.qs { display: flex; flex-direction: column; gap: 1.1rem; }
.q { font-size: 1.7rem; font-weight: 600; padding-left: 1.2rem; border-left: 4px solid var(--dark-blue); }
.qs .verdict { margin-top: 0.8rem; font-size: 1.5rem; color: var(--white-sub); font-weight: 400; }
</style>

<!--
Сами тулы и сервер написаны правильно, я её показывал минуту назад. Агент просто не знает, что инструменты можно чейнить, объединять в цепочку.

[click] агент видит из описаний, что есть поиск, есть адрес, есть инфо о товаре, всё по отдельности

[click] но без явных шаманств с промптингом или кастомным скиллом 

[click] мы чаще всего получим просто выдачу одного из вызванных инструментов. 
[click] Причём всё равно в текстовом формате - пользователь видит тупо текст и ссылку на внешний сервис. Можно ли лучше? Конечно! 

-->

---

# Два ресурса

<div class="split">
  <div class="half" v-click="1">
    <div class="uri cyan"><code>ui://</code></div>
    <div class="fix">интерфейс для человека</div>
  </div>
  <div class="half" v-click="2">
    <div class="uri accent"><code>skill://</code></div>
    <div class="fix">инструкция для агента</div>
  </div>
</div>

<div class="foot" v-click="3">И то и другое — <b>resources</b> в MCP</div>

<style scoped>
.split { margin-top: 2.4rem; display: grid; grid-template-columns: 1fr 1fr; gap: 2rem; }
.half { padding: 2rem 1.8rem; border: 1px solid rgba(255,255,255,0.16); border-radius: 14px; background: rgba(255,255,255,0.04); }
.uri code { font-size: 2.6rem !important; font-weight: 800; background: transparent; }
.uri.cyan code { color: var(--color-cyan); }
.uri.accent code { color: var(--color-green); }
.fix { margin-top: 1.1rem; font-size: 1.7rem; font-weight: 700; }
.foot { margin-top: 2.2rem; font-size: 1.8rem; }
</style>

<!--
У нас в 2026 году появилось две возможности сделать эту выдачу красивее, удобнее и точнее

[click] Для людей мы теперь можем отрисовать живой виджет в айфреймах, где человек может посмотреть на товар, галерею фото покрутить, посмотреть табличку характеристик и вернуться обратно в выдачу, отфильтровать её, вернуться обратно

[click] А проблему чейнинга, организации вызовов инструментов в цепочки, мы можем решить, отдавая кастомный скилл через ресурс — skill://.

[click] Рабочая группа Skills Over MCP формулирует это так: скиллы — это про управление контекстом, а MCP — протокол контекста. Агент уже ходит на сервер за тулами. За инструкцией он может ходить туда же.

-->
