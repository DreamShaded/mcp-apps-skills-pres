---
layout: simple-slide
variant: 11
---

# Осень 2025: развилка

<div class="fork2">
  <div class="side" v-click="1">
    <div class="nm cyan">mcp-ui</div>
    <div class="who">Ido Salomon · Liad Yosef</div>
    <div class="d">своя схема сообщений · UI в результате тула</div>
    <div class="adopt" v-click="2">Postman · Shopify · Hugging Face · Goose · ElevenLabs</div>
  </div>
  <div class="side" v-click="3">
    <div class="nm accent">OpenAI Apps SDK</div>
    <div class="who">6 октября 2025 · DevDay</div>
    <div class="d">тот же MCP под капотом · своя реализация</div>
  </div>
</div>

<div class="bottom" v-click="4">два несовместимых способа делать одно и то же</div>

<style scoped>
.fork2 { margin-top: 1.4rem; display: grid; grid-template-columns: 1fr 1fr; gap: 2rem; }
.side { padding: 1.4rem 1.6rem; border: 1px solid rgba(255,255,255,0.14); border-radius: 12px; background: rgba(255,255,255,0.04); }
.nm { font-size: 2rem; font-weight: 800; }
.who { margin-top: 0.5rem; font-size: 1.35rem; color: var(--white-sub); }
.d { margin-top: 0.8rem; font-size: 1.4rem; }
.adopt { margin-top: 1rem; font-size: 1.25rem; color: var(--white-sub); }
.bottom { margin-top: 1.8rem; font-size: 1.8rem; font-weight: 700; }
</style>

<!--

А в конце хочется провести ретроспективу
Осенью прошлого года двое разработчиков, Идо Саломон и Лиад Йосеф, сделали mcp-ui. [click] Сообщество, свой протокол сообщений, UI приезжает прямо в результате тула.

[click] Проект взлетел: Postman, Shopify, Hugging Face, Goose, ElevenLabs.

[click] Шестого октября OpenAI на DevDay показывает Apps SDK. Под капотом тот же MCP, реализация своя.

[click] Дальше знакомая картина: два несовместимых способа сделать одно и то же, и разработчик сервера пишет адаптеры под каждый хост.

-->

---

# Слияние

<div class="merge">
  <div class="ev" v-click="1"><b>21 ноября 2025</b> · SEP-1865<span>Anthropic + OpenAI + мейнтейнеры mcp-ui</span></div>
  <div class="ev" v-click="2">предобъявленный ресурс · JSON-RPC вместо своего формата · обязательный sandbox</div>
  <div class="ev" v-click="3"><b>26 января 2026</b> · первое официальное расширение MCP<span>io.modelcontextprotocol/ui</span></div>
  <div class="ev" v-click="4">ChatGPT · Claude · VS Code · Goose</div>
</div>

<style scoped>
.merge { margin-top: 1.4rem; display: flex; flex-direction: column; gap: 1.1rem; }
.ev { font-size: 1.6rem; font-weight: 600; padding-left: 1.3rem; border-left: 4px solid var(--dark-blue); }
.ev b { color: var(--color-cyan); }
.ev span { display: block; margin-top: 0.3rem; font-size: 1.3rem; font-weight: 400; color: var(--white-sub); }
</style>

<!--
[click] Двадцать первого ноября случается то, чего в такой ситуации обычно не случается. Anthropic, OpenAI и мейнтейнеры mcp-ui садятся вместе и публикуют SEP-1865. Не «мы сделали свой стандарт», а слияние двух живых экосистем в одну спеку. В авторах — core-мейнтейнеры MCP из OpenAI и Anthropic, и оба автора mcp-ui.

[click] Ключевые решения вы уже видели. Ресурс объявляется заранее, а не приезжает в результате тула. Свой формат сообщений выбросили в пользу обычного JSON-RPC. Песочница обязательна.

[click] Двадцать шестого января MCP Apps выезжает как первое официальное расширение MCP. Идентификатор io.modelcontextprotocol/ui.

[click] Рендерят ChatGPT, Claude, VS Code, Goose. mcp-ui при этом никуда не делся: остался рекомендованным клиентским SDK.
-->

---

# Спор про скиллы

<div class="debate">
  <div class="col" v-click="1"><div class="sep cyan">SEP-2076</div><div>первоклассный примитив · skills/list · skills/get · свои capabilities</div></div>
  <div class="arg" v-click="2">за: ресурсы application-controlled, а скилл model-controlled по природе</div>
  <div class="col" v-click="3"><div class="sep accent">SEP-2640</div><div>расширение поверх Resources · ядро протокола не трогаем</div></div>
  <div class="win" v-click="4">победила вторая линия · PR-2076 закрыт</div>
</div>

<style scoped>
.debate { margin-top: 1.2rem; display: flex; flex-direction: column; gap: 1rem; }
.col { padding: 1rem 1.3rem; border: 1px solid rgba(255,255,255,0.14); border-radius: 10px; background: rgba(255,255,255,0.04); font-size: 1.4rem; }
.sep { font-size: 1.6rem; font-weight: 800; }
.arg { font-size: 1.35rem; color: var(--white-sub); padding-left: 1.3rem; }
.win { font-size: 1.55rem; font-weight: 700; }
.cost { font-size: 1.5rem; font-weight: 700; color: var(--color-green); }
</style>

<!--
Со скиллами история другая, в ней был настоящий спор. Расскажу, потому что он объясняет, почему у скиллов до сих пор шероховато.

[click]  «Agent Skills as a First-Class MCP Primitive». Предлагал сделать скилл отдельным примитивом протокола рядом с tool/prompt/resource, со своими методами skills/list, skills/get, своей capability и уведомлением list_changed. Плюсы — чистая capability-негоциация и отдельные нотификации. 

[click] Аргумент был сильный. Ресурсы в MCP по умолчанию application-controlled: решение прочитать принимает хост. А скилл, который учит агента оркестрировать тулы, по природе model-controlled — агент должен сам решить, что ему нужна инструкция.

[click] Победила другая линия, «Skills Extension»: расширение поверх Resources. Ядро протокола не трогаем, любой существующий сервер апгрейдится за вечер. скилл раздаётся через уже существующий примитив Resources под схемой skill://, каждый файл скилла — обычный ресурс. Директорийная модель сохраняется, хост, который уже умеет читать ресурсы как виртуальную файловую систему, потребляет скиллы тем же кодом. Это текущее направление рабочей группы Skills Over MCP.

-->

---
layout: two-cols
variant: 11
leftWidth: 44%
align: top
---

::header::

# Что не работает и где мы сейчас

::default::

<div class="bad" v-click="1">
  <div class="h">модели ленивы</div>
  <div>агент игнорирует скилл, идёт в тулы, падает, потом читает</div>
  <div class="fix">лечит строка в instructions: «сначала прочитай скилл»</div>
</div>

::right::

<div class="good" v-click="2">
  <div class="h">кто уже сделал</div>
  <div class="chips">
    <span class="chip">FastMCP 3.0</span>
    <span class="chip">Microsoft Agent Framework</span>
    <span class="chip">GitHub MCP</span>
    <span class="chip">Mintlify</span>
    <span class="chip">Apify</span>
  </div>
</div>

<div class="status" v-click="3">Apps — продакшен с 26 января · Skills — SEP-2640, pending</div>

<style scoped>
.bad { padding: 1.2rem 1.4rem; border-left: 4px solid #ff5a5a; background: rgba(255,255,255,0.03); }
.bad .h { font-size: 1.7rem; font-weight: 700; color: #ff8a8a; }
.bad div { font-size: 1.35rem; margin-top: 0.5rem; }
.bad .fix { color: var(--white-sub); }
.good .h { font-size: 1.6rem; font-weight: 700; margin-bottom: 0.9rem; }
.good .chip { font-size: 1.15rem; padding: 0.5rem 1rem; }
.status { margin-top: 1.6rem; font-size: 1.45rem; font-weight: 600; color: var(--color-green); }
</style>

<!--
Честно про то, что не работает.

[click] Боб Дикинсон на своём инструменте наблюдал: агент видит доступный скилл, игнорирует его и лезет прямо в тулы. Падает несколько раз. И только потом читает инструкцию. Помогла строка в instructions сервера: сначала прочитай скилл.

[click] При этом скиллы поверх MCP — не бумага. FastMCP отдаёт их штатно. Microsoft встроила в Agent Framework, для .NET и Python. GitHub MCP Server подтягивает скиллы из репозиториев. Mintlify отдаёт и через well-known, и как MCP-ресурсы. Прототипы хостов есть в gemini-cli, goose, codex, Claude Code.

[click] Статус на сегодня. MCP Apps — продакшен, работает с двадцать шестого января. Skills over MCP — SEP-2640, всё ещё pending. Половина моего доклада — то, что можно катить завтра. Половина — то, за чем надо следить.
-->
