---
layout: simple-slide
variant: 11
---

# Что чем закрывать

<table class="dec">
  <tbody>
    <tr v-click="1"><td class="need">данные меняются между вызовами</td><td class="use"><b class="cyan">tool</b> · детерминированный вызов по схеме</td></tr>
    <tr v-click="2"><td class="need">знание стабильно: «как пользоваться сервером»</td><td class="use"><b class="accent">skill://</b> · текст, модель может понять неправильно</td></tr>
    <tr v-click="3"><td class="need">человек выбирает, сравнивает, подтверждает</td><td class="use"><b>ui://</b> поверх тула</td></tr>
  </tbody>
</table>

<div class="trade" v-click="4">скилл правится словами, но ценой недетерминированности</div>

<style scoped>
.dec { margin-top: 1.4rem; border-collapse: collapse; font-size: 1.5rem; width: 100%; }
.dec td { padding: 1rem 1.3rem; border: 1px solid rgba(255,255,255,0.12); vertical-align: middle; }
.dec .need { color: var(--white-sub); width: 44%; }
.dec .use b { font-weight: 800; }
.trade { margin-top: 1.8rem; font-size: 1.6rem; font-weight: 700; }
</style>

<!--
Слайд, ради которого можно было прийти на доклад.

[click] Данные меняются между вызовами — нужен тул. Схема, типы, детерминированный вызов.

[click] Знание стабильно неделями и объясняет, как пользоваться сервером — нужен скилл.

[click] Человек должен выбрать, сравнить или подтвердить — нужен виджет поверх тула.

[click] Размен назову вслух, потому что он есть. Скилл покупает возможность править поведение словами ценой недетерминированности. Тул исполняется одинаково всегда. Скилл модель интерпретирует, и без строки в instructions она может пойти мимо него. Если решение выглядит так, будто ничего не отдаёшь, значит, размен просто не нашли.
-->

---
layout: two-cols
variant: 11
leftWidth: 50%
align: top
---

::header::

# Границы доверия

::default::

<div class="tbox ui" v-click="1">
  <div class="t cyan"><code>ui://</code></div>
  <div>sandbox обязателен · сети нет · вызовы аудируемы · подтверждение</div>
  <div class="sub">модель безопасности встроена в спеку</div>
</div>

::right::

<div class="tbox sk" v-click="2">
  <div class="t accent"><code>skill://</code></div>
  <div>текст с удалённого сервера едет в контекст как инструкции</div>
  <div class="sub">непрямая инъекция промпта</div>
</div>

<div class="cav" v-click="3">нового уровня доверия скиллы не создают</div>

<style scoped>
.tbox { padding: 1.3rem 1.5rem; border-radius: 12px; background: rgba(255,255,255,0.04); border: 1px solid rgba(255,255,255,0.14); }
.tbox .t code { font-size: 1.9rem !important; font-weight: 800; background: transparent; }
.tbox div { font-size: 1.35rem; margin-top: 0.6rem; line-height: 1.35; }
.tbox .sub { color: var(--white-sub); }
.cav { margin-top: 1.4rem; font-size: 1.45rem; font-weight: 700; }
.req { margin-top: 1rem; font-size: 1.3rem; color: var(--white-sub); }
</style>

<!--
Модели безопасности у двух ресурсов разные, и это стоит развести.

[click] У виджетов защита встроена. Песочница обязательна. Сети нет, пока сервер не задекларирует домены. Каждый вызов из UI проходит через хост, логируется и может потребовать подтверждения.

[click] У скиллов иначе. Вы пускаете текст с удалённого сервера прямо в контекст модели, и это инструкции, а не данные. Непрямая инъекция промпта — вредоносные указания приезжают вместе с контентом.

[click] Здесь я сразу оговорюсь. Черновик спеки говорит: нового уровня доверия или недоверия скиллы не создают. Человек, подключивший сервер, уже расширил на него границу доверия. Вредоносный сервер навредит через тулы не меньше, чем через текст.

[click] Требования при этом конкретные. Хост не исполняет ничего локально из содержимого скилла без явного согласия: ни хуков, ни скриптов, ни шелл-команд. Хост показывает, с какого сервера приехал скилл, и даёт посмотреть содержимое до загрузки. Microsoft скрипты из архивных скиллов не исполняет вообще.
-->

---

# Итог:

<div class="end">
  <div class="was" v-click="1">MCP-сервер сегодня — API без фронтенда и без документации</div>
  <div class="two">
    <div class="r" v-click="2"><code class="cyan">ui://</code> — интерфейс для человека</div>
    <div class="r" v-click="3"><code class="accent">skill://</code> — инструкция для агента</div>
  </div>
</div>

<style scoped>
.end { margin-top: 1.6rem; }
.was { font-size: 1.7rem; font-weight: 600; }
.two { margin-top: 2rem; display: flex; flex-direction: column; gap: 1.2rem; }
.r { font-size: 1.8rem; font-weight: 600; }
.r code { font-size: 2rem !important; font-weight: 800; background: transparent; }
.r .cyan { color: var(--color-cyan); }
.r .accent { color: var(--color-green); }
.repo2 { margin-top: 2.4rem; font-size: 1.4rem; color: var(--white-sub); }
</style>

<!--
Вот с чего мы начали.

[click] Типовой MCP-сервер сегодня — это API без фронтенда и без документации. Тулы у него есть, а человеку показать нечего и агенту объяснить нечем.

Чинится это двумя ресурсами.

[click] ui:// — интерфейс для человека.

[click] skill:// — инструкция для агента.

[click] Оба лежат в репозитории, ссылка на экране.

Спасибо.
-->

---
layout: two-cols
variant: 4
leftWidth: 40%
transition: slide-up
align: center
---

::default::
<img :src="'/images/slides/my_qr.png'" class="h-80 rounded-xl shadow-lg border border-main mx-auto mt-4" />

::right::
<TextBig>
  Спасибо
</TextBig>

<!--

-->
