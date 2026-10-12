# AdSpyLab — разбор функций (исследование 2026-10-12)

Как собрано: отдельный Chromium (Playwright) на Mac владельца, вход выполнил владелец, аккаунт на триале тарифа All-inclusive. Обойдены все разделы дашборда: текст страниц, скриншоты и JSON-ответы API приложения, плюс публичные страницы и публичный файл MCP-скилла. Ничего не менялось: биллинг, команда, ключи и формы не трогались. После работы сессия браузера закрыта, профиль удалён.

Пометки: **[видел]** — видел в интерфейсе или в ответе API; **[предположение]** — вывод, который не проверялся напрямую.

Скриншоты (7 шт.: overview, monthly stats, карточка креатива, concept families, воронка, таблица воронок, каталог экранов) остались на машине владельца в `adspylab-research/screenshots/` worktree ads-monitor.

## 1. Обзор продукта и позиционирование

- Слоган на лендинге: «Know what to test next in web2app». Подзаголовок: «WEB2APP COMPETITIVE INTELLIGENCE». **[видел]**
- Ниша — только web2app-подписки (квизы, лендинги, пейволы). Это не общий ad-spy, а отраслевая база с готовой разметкой: бренды, категории, воронки, темы и форматы креативов.
- Четыре продукта: Analytics (рынок), Ads Library (креативы), Funnels Library (воронки), AI agent + MCP.
- Целевые роли на лендинге: «Growth & UA teams», «Creative strategists», «Product teams & founders».
- Юрлицо в футере: Mobfunnels Limited. Оплата через Stripe; в коде есть следы старой продажи через Gumroad. **[видел]**
- Про spend продукт честно говорит «нет». FAQ дословно: «No. Analytics shows advertising activity and changes in ad volume. Use these signals to choose what to research next; they are not estimates of spend, revenue or conversions.»

## 2. Карта навигации

Верхняя панель (сайдбара нет): `Overview` · `Monthly Stats` · `Ads Library` · `Funnels Library` · email · `Settings` · `Logout`.

| Раздел | URL |
|---|---|
| Overview | `/dashboard/overview` |
| Monthly Stats | `/dashboard/monthly-stats` |
| Профиль бренда | `/dashboard/brand?brand=<имя>&source=fb\|google` |
| Ads Library | `/dashboard/adslibrary` (Google: `?platform=google`, карточка: `?asset=<id>`) |
| Funnels Library | `/dashboard/funnels` (вкладка: `?tab=funnels`, воронка: `#run=<id>`) |
| AI agent | `/dashboard/agent` — в меню нет, заглушка |
| Settings | `/settings` |
| Credits & usage | `/settings/usage` |
| Changelog | `/dashboard/changelog` — маршрут есть, страница пустая |

Публичные: `/` (лендинг с тарифами, FAQ и проверкой «Is your competitor already here?»), `/reports/`, `/niches/`, `/brands/`, `/guides/` (EN и RU), `/terms`, `/privacy`, `/refunds`, `/mcp/skill/SKILL.md`, `/mcp/skill.zip`.

В коде есть закрытые маршруты админки (`/backstage`, `/admin/recordings`): каталог брендов, очередь кандидатов в бренды, парсеры, домены, пользователи, разделы TikTok и YouTube. Не открывались. **[видел маршруты в публичном бандле; содержимое — предположение]**

## 3. Analytics → Overview

**Назначение:** за минуту понять, кто в рынке растёт и что появилось нового.

**Что показывает [видел]:**
- Плашка «Advertising activity» — число объявлений за последний полный месяц (Meta, сентябрь 2026: 1 349 209; Google: 87 250).
- График «Monthly advertising volume» — помесячный объём с января 2025, топ-20 плюс «Others». Разрез: `Whole market` / `Categories` / `Brands` / `Domains` / `FB Pages`.
- Таблица «Explore the players» — топ-20 за шесть полных месяцев. Колонки: имя, `Total`, шесть месяцев, `Last M %`, `Last 3M %`. Сортировка по любой колонке, порог «Min ads in <месяц>».
- «Recent updates · Last 7 days» — три списка по 20: `New brands`, `New funnels`, `New fan pages` с датой первого появления.

**Фильтры:** `Period` (All available months / Last 3 / 6 / 12 complete months / Custom), `Meta Ads` / `Google Ads`, `Brand`, `Category`, под «More filters» — `Domain`, `FB Page`, `Funnel Name`, `Language`.

**Drill-down:** бренд → профиль бренда; воронка → Monthly Stats с фильтром по имени воронки; страница → facebook.com.

**Подсказки (дословно):** «Complete calendar months of advertising activity.», «Total ads over the six displayed months».

## 4. Analytics → Monthly Stats

**Назначение:** сводная таблица объёма рекламы по месяцам с группировкой до трёх уровней.

**Что показывает [видел]:**
- Строка = сущность, колонки: `Total`, `3M growth`, `MoM`, затем 13 месяцев. Текущий месяц помечен `*`. Ячейки подкрашены по строке (тепловая шкала).
- `Group by` — Meta: Brand, Domain, FB Page, Category, Language, Funnel Name, Topic, Subtopic, Format, Creative type; вторым и третьим уровнем ещё `Funnel URL` и `Creative family`. Google: Brand, Domain, Creative Type, Legal Entity, Country, Category.
- Метрика: `Ads` или `Visuals` (уникальные визуалы).
- Порог «Min ads · last complete month», поиск «URL contains…», пагинация по 50 строк, ширина колонок меняется перетаскиванием.
- У бренда ссылка `Profile ↗`.

**Подсказки (дословно):**
- «Ads counts advertising objects. One visual can appear in multiple ads. Colour compares complete months within each row. * Current month is incomplete.»
- 3M growth: «Last three complete calendar months vs the preceding three; selected filters apply».
- MoM: «Last complete calendar month vs the previous month; selected filters apply».
- Текущий месяц: «Current month — still filling, not a complete month yet».

**Под капотом [видел]:** `GET /api/dashboard/monthly-stats?entity_type=…&page=…&limit=…&min_monthly_ads=…`, раскрытие строки — `/api/dashboard/monthly-stats/drilldown?group_by=…&brands=…` (у BetterMe по воронкам 370 строк). Объявление считается в месяце своего запуска.

## 5. Профиль бренда

**Назначение:** всё по одному бренду на одной странице.

**Что показывает [видел, на примере BetterMe, апрель–сентябрь 2026]:**
- Три цифры: «Ads across selected months» (697 674), «Latest complete month» (155 147), «Change vs previous month» (−3 %, −4 224 ads).
- График с разрезом `Whole brand` / `Domains` / `Pages` / `Funnels` / `Languages`.
- «Top by domains → funnels» — топ-20 пар домен–воронка с долями.
- «Top FB Pages» — топ-20 страниц с долями (у BetterMe это «Lessie Fitness» 34,6 %, «BetterMe Men» 18,2 % и т. д.).
- «Top Languages» — топ-20 языков.
- «TOP FUNNEL» — превью главной воронки: «64 saved screens · Captured Sep 28, 2026», шесть первых экранов с типами.
- «Top creatives to explore» — шесть креативов со ссылкой в Ads Library.
- Переключатель `Meta` / `Google`, выбор месяцев «From» / «To».

**Оговорка в интерфейсе (дословно):** «Counts follow Monthly Stats. An ad can appear in several months; the period total is not a count of unique ads. The current month is still incomplete.»

## 6. Ads Library (Meta)

**Назначение:** найти рабочие креативы и концепции конкурентов.

### 6.1 Вкладка `Creatives`

Плитка = один креатив; файлы, одинаковые на глаз, склеены. **[видел]**

- На плитке: превью (видео играет, есть «Unmute video»), тип и длительность, бренд (`+N`, если брендов несколько), четыре числа: `Ads`, `EU reach`, `Rank`, `Days`.
- Виды: `Grid` и `Table`. Колонки таблицы: creative, brand, ads, launched 7d, launched 14d, EU total, ΔEU 7d, ΔEU 14d, rank, ΔRank 7d, ΔRank 14d, active 7d, active 14d, duration, first seen, last seen. Колонки включаются, переставляются и растягиваются («⚙ columns»).
- Подгрузка по 60, кнопка «Show more».

**Фильтры [видел]:** `Period` (All available months / Last 90 / 30 / 14 / 7 days / Custom — по дате первого запуска), `Meta Ads` / `Google Ads`, `Brand`, `Category`, `Topic` (227 тем), `Subtopic` (550), `Format` (30), `Type` (video / image), `Domain`, `FB Page`, `Funnel Name`, `Language`, «URL contains…» (от 3 символов, разделители игнорируются: «taichi» находит «tai-chi»).

**Поиск:**
- `Text` — «Search ad text, transcripts, brands and domains. Use quotes for an exact phrase.»
- `Meaning` — «Find creatives by what is shown and said. Press Enter to search.» Режимы `Both` / `Picture` / `Speech`. Появляется сортировка `Relevance`.

**Сортировки (14):** Most ads (all time); Most ads launched in 7 / 14 days; Highest cumulative EU reach; Biggest EU reach gain in 7 / 14 days; Highest rank on its page; Climbed the page rank most in 7 / 14 days; Most ads active within 7 / 14 days; Longest-running (first → last day); Newest first (by first appearance); Recently seen alive.

**Metric filters:** порог «≥ N» (для rank «≤ N») по любой метрике, применяется до сортировки.

**Saved views:** сохраняет вкладку, фильтры, сортировку, пороги и колонки под именем — только в этом браузере (так сказано в Metric guide).

### 6.2 Карточка креатива

Открывается модальным окном, адрес `?asset=<id>`, кнопка «copy link». **[видел]**

- Слева плеер и посадочный URL с UTM-шаблонами и кнопкой «copy».
- `Details`: Status («Active · checked 10.10.2026»), First seen, Last seen, Creative copies (3 954, +1 за 7 дней, +14 за 14), EU Reach (57k, +100, +6k), Rank (#8), Languages, Concept family со ссылкой «open family →».
- Три мини-графика: «launches per week», «EU reach (cumulative)», «rank on page (#1 — top)».
- `Script` — расшифровка речи: «speech language: Spanish (100%)», «speech 175s of 177s», полный текст.
- `Texts` — все тексты объявлений с числом повторов («1 370× …»), на всех языках.
- `Links` — домены, бренд, категория, воронки с числом объявлений.
- `Pages` — страницы Facebook: имя, page id, число объявлений, ссылка в Ad Library.
- `Meta Ads Library` — список объявлений («Ads (3 954, showing 30)») с датами и ссылками на оригинал.
- «Similar ads»: ряд «Same hooks» и ряд «Same body» с процентом сходства (до 96 %).

### 6.3 Вкладка `Concept families`

Строка = концепция: пара «hook × body» у видео или семейство дизайнов у статики. **[видел]**

- У семейства есть название и описание, написанные моделью (поле `label_source: "llm"`). Пример: «Dialogue about skeptical son: seated tai chi transforms father in month».
- Свои метрики: hook var., body var., creatives, Strong candidate, Survived 14d и %, Potential winners и %.
- Свои сортировки: Most hook variations, Most body variations, Most creatives, Strong candidate (estimated score), Most ads that survived 14+ days, Highest share…, Most potential winners, Highest share of potential winners.

### 6.4 Metric guide — тексты дословно

| Метрика | Текст |
|---|---|
| ads | Ads (all time) |
| launched 7d / 14d | Ads launched in the last 7 / 14 days |
| EU total | Cumulative EU reach: the last known cumulative of each ad, summed. EU-targeted ads only |
| ΔEU 7d / 14d | EU reach gained over the last 7 / 14 days. An ad launched inside the window had no reach before it, so its whole total counts as the gain |
| rank | Best position in the Ad Library listing of its OWN page (#1 = most delivery); never comparable across pages |
| ΔRank 7d / 14d | Rank move vs 7 / 14 days ago; up = climbed its page listing |
| active 7d / 14d | Ads alive on at least one of the last 7 / 14 days |
| duration | Days from first appearance to the last day seen alive |
| Strong candidate | Estimated 0-100 score that surfaces strong candidates, blending several delivery metrics; 0 = no signal yet |
| Survived 14d | How many ads, of those old enough to be judged (launched 14+ days ago), lived 14+ days from launch |
| Survived 14d % | Survived 14d as a share of the judged ads. Families with fewer than 5 judged ads rank last and never pass a percent threshold; equal shares rank the bigger family first |
| Potential winners | Creatives with positive evidence: top-10% of a big page listing, 10k+ EU total, +5k EU in 14d, or lived 14+ days and still active |
| first seen | First appearance in the archive |
| last seen | Last day seen alive: ad cards, daily listing or a fresh active status |

Блок «Read with care» (дословно):
- «Rank is a position inside the advertiser's OWN page listing — it is never comparable across pages or brands.»
- «EU reach exists only for ads that ran in the EU; «—» means no EU delivery, not zero performance.»
- «Survived 14d counts only ads old enough to be judged (launched ≥ 14 days ago). Most ads are switched off within two weeks — compare within a brand or category, not across.»
- «Ads counts ad objects: one visual can run as hundreds of ads. «creatives» counts distinct visuals.»
- «With exactly one brand selected, the numbers are that brand's share of a creative; with several brands or none, they are the creative's totals.»

Про дату (дословно): «When the creative first went live — the launch date of its earliest ad. Monthly Stats groups by the same thing, one month per ad; here a creative that ran again later still sits under its first launch.»

### 6.5 Справочники тем и форматов [видел]

- Топ тем: Tai chi (1 572 516 объявлений), Calisthenics (812 526), Capsule wardrobe & outfit styling, Pilates, Pelvic floor & Kegel training, Communication…, **Anxiety, stress & nervous system (205 911)**, Walking, Fasting & meal timing. Из ментального здоровья дальше: IQ test, Burnout (109 143), Procrastination & self-discipline (91 799), ADHD (adult) (82 351), Childhood trauma & inner child (75 894), Jungian archetype tests.
- Топ форматов: Text hook over footage (video, 1 802 758), UGC talking head (1 192 619), Text on designed background (static, 1 007 450), Static banner / quiz, Podcast / stage speech, Sketch / dialogue, Hero image + headline, Cinematic drama, 3D cartoon drama, Grid / listicle… всего 30.

## 7. Ads Library (Google)

Отдельный вид с другими полями. **[видел]**

- Заголовок: «157 299 videos · 422 920 statics · 981 494 ads».
- Плитка: тип, название семейства, рекламодатель (юрлицо), число объявлений, число стран, дней в показе.
- Сортировки: Most ads running this video; Most impressions in the EEA; Most re-uploads of the same video; Most countries; Longest-running; Longest video; Newest first; Recently shown. Переключатель `Merge re-uploads` / `Every upload`.
- Карточка: вкладки `Details`, `Texts`, `Links`, `Google Ads Transparency`; поля First shown, Last shown, Ads, «Shown in EEA» диапазоном (51k–755k), Countries, Concept family, график «new ads per week».
- Фильтры в Analytics для Google: Domain, Creative Type (image / video / text), Legal Entity, Country.

## 8. Funnels Library

**Назначение:** посмотреть воронку конкурента экран за экраном вплоть до цены, не проходя её самому.

Раздел — встроенное отдельное приложение (iframe `/funnels/?embed=1`, свой API `/funnels/api/*`). **[видел]**

### 8.1 Вкладка `Catalog` — экраны

- Сетка скриншотов экранов: бренд, номер экрана, тип, ссылка «View funnel →». Для BetterMe — 3 233 экрана.
- Фильтры: `Brand`, `Category`, `Domain`, `FB Page`, `Funnel Name`, «URL contains…», «Search screen text…», `Screen type`, `Funnel language` (25 значений, по умолчанию English), `Trackers & platforms` (86 значений).
- `Screen type`: 1st screen, 2nd screen, Emails after signup (4650), Question, Info, Input, Multiple choice, Email capture, Paywall, Checkout, Story, Loading, Downsell.
- Объёмы по типам (из API): question_single 75 697; info 28 573; input 18 863; question_multi 15 267; email_gate 6 341; **paywall 1 826; checkout 1 781**; story 1 505; loader 1 166; downsell 45.
- Поиск по тексту экранов работает: запрос «anxiety» — 377 экранов.

### 8.2 Вкладка `Funnels` — таблица воронок

Колонки: Brand, Funnel (домен, язык), Screens, Ads, «Capture date / result» (например «Oct 5, 2026 · Checkout»), Subscriptions (периоды 1w / 4w / 12w с ценами), Advertising (пиксели), Payments, Analytics, Software. Сортировка: Ads, Screens, Newest first, Brand. Для BetterMe — 56 воронок.

### 8.3 Страница воронки

- Шапка: «BetterMe · flow-4684», «EN · Captured: Sep 2, 2026 · Checkout».
- Выбор версии прохода: шесть дат (2, 21, 28 сентября, 3, 5, 5 октября) с числом экранов — видна история.
- Полотно со всеми экранами по порядку, масштаб и «Fit»; у длинных страниц пометка «↕ Full page».
- Правая панель «Technology»: Advertising trackers, Payment providers / trackers, Analytics, Software & frameworks.
- Правая панель «Pricing & subscriptions»: выбор экрана с ценой и планы (1w / 4w / 12w, например «4w €6.99»).

### 8.4 Письма после регистрации

`Emails after signup` — 4 650 писем. **[видел]** У письма: бренд, смещение от регистрации («+6 d 17 h»), тема, отправитель, превью текста, текст и ссылка кнопки, скриншот письма и скриншот страницы, куда ведёт письмо. То есть они оставляют email в воронке и собирают цепочку дожима.

### 8.5 Под капотом воронок [видел в API]

- В базе: 3 292 воронки (проходов), 151 064 экрана, 69 337 картинок, 241 бренд, 342 домена.
- Список без фильтра языка вернул 927 воронок. Исход прохода: terminal 820, stuck 72, dead_link 13, blocked 12, max_steps 7, error 2. Конец воронки: paywall 493, checkout 208, остальные оборвались раньше.
- Цены найдены у 464 из 927, письма — у 204.
- Все 927 записей помечены рынком `DE`, цены в основном в евро; у одной версии BetterMe рынок `US` и цены в долларах. **[предположение]** Обход идёт в основном с немецкого IP, американский — выборочно.
- Дата последней проверки у 634 воронок — 2026-10-06. **[предположение]** Перепроход раз в неделю (в именах прогонов: `rewalk_20260928`, `rewalk_20261005`).
- Есть сравнение версий: «text changed on 5 screen(s)», «picture changed on 2 screen(s)», «removed screen '…' (checkout)».
- Цена хранится структурно: тип (subscription / trial), фазы с суммой, валютой и днями, бейдж («Most popular»), флаг предвыбора.
- У экрана: тип, вопрос, заголовки, URL шага, скриншот и миниатюра, флаг дубликата.
- Поиск по экранам: модель `sentence-transformers/all-MiniLM-L6-v2` (384 измерения), кластеры через TF-IDF/SVD и MiniBatchKMeans.

## 9. AI agent

- В интерфейсе — заглушка: «AI agent is coming soon. Research competitors with the AdSpyLab agent. It is coming soon.» В меню пункта нет. **[видел]**
- При этом лендинг продаёт его как часть All-inclusive, а в ответе API у аккаунта есть признак `agent`.
- В коде есть маршруты чата (`agent/c/:id`), отчётов (`agent/r/:id`) и публичной ссылки (`/share/:token`), а также типы отчётов с русскими названиями: «разбор бренда», «сравнение за месяц», «список наблюдения», «поиск по нише». **[видел в бандле; как это работает — предположение]**
- Проверить ответы агента было нельзя.

## 10. MCP / API

**Что видно в Settings [видел]:**
- «MCP access — Connect your AI tools to AdSpyLab research.»
- «Connector link → Generate link»: «Generate a personal link to connect your AI agent. The link is a secret — anyone who has it can spend your row limits.» Ссылка не генерировалась.
- «Research playbooks (Skill) → Download skill» — zip для claude.ai и Claude Code. `SKILL.md` лежит публично.
- Отдельного REST API с ключами для клиентов нет — только MCP-коннектор по личной ссылке. **[предположение по отсутствию в интерфейсе]**

**Инструменты MCP (из публичного SKILL.md) [видел]:** `brand_profile`, `market_rankings`, `monthly_stats`, `research_filters`, `recent_updates`, `funnels_library_search`, `funnels_library_detail`, `competitor_report`, `category_report`, `funnel_report`, `funnel_map`, `creative_shortlist`, `ads_library_search`, `ads_library_top`, `queued_query`, `suggest_capability`; ресурс `adspylab://categories`. Средняя часть файла обрезалась при чтении — список может быть неполным.

**Правила из скилла, полезные как методология (дословно):**
- «Volume is a count of ads, not spend. A drop has many possible causes (domain or page switch, ban, budget, coverage) - report the change, do not assert why.»
- «`eu_reach_*` (actual reach gained; visible ONLY for EU-targeted ads - null means "not visible", not zero)».
- «`page_rank` / `rank_gain_*` (an ordinal position inside ONE advertiser page - never a proxy for reach or spend, never compared across advertisers)».
- «A funnel name identifies a funnel only INSIDE one advertiser.»
- «Tools read existing captures and do not start crawling.»

**Кредиты [видел в `/settings/usage`]:**
- «1 credit = 1 creative, with its media, statistics and metadata»; «1 credit = 1 screen, with its preview and metadata»; «1 credit = 1 object × 1 month, including standard metrics».
- «Only returned data counts. Duplicate items within one response count once; separate requests count again. Failed and empty requests are free.»
- Страница показывает расход по дням, по инструментам, по участникам и журнал запросов.

## 11. Settings

**[видел]**
- Account (email), Subscription («Active · Via Stripe», кнопка «Manage Subscription»).
- Team: «Team (1 of 3) — Share your subscription with up to 2 teammates.» Доп. место — $49 в месяц.
- Active Sessions: «2 of 2» — одновременно не больше двух сессий, лишнюю можно отозвать.
- Change Password, Support (форма с вложением до 5 МБ).
- Language: только English, «Coming soon».

## 12. Тарифы и лимиты

| Тариф | Цена | Что входит |
|---|---|---|
| Analytics | $490 / мес | Overview, Monthly Stats, профили брендов, Meta и Google |
| Ads Library | $490 / мес | Креативы, concept families, поиск по тексту и смыслу |
| Funnels Library | $490 / мес | Экраны воронок, квизы, пейволы |
| All-inclusive | $999 / мес | Всё выше + MCP + AI agent, 10 000 кредитов, 3 места |

- Доп. место: $49 / мес. Пакеты кредитов (из API): 5 000 за $199, 15 000 за $499, 40 000 за $999. На странице usage при этом написано «Additional credits are not available to purchase yet». **[видел]**
- Триал: 1 000 кредитов. На момент исследования у аккаунта: план `bundle`, конец триала 2026-10-12 11:01:29 UTC, статус подписки в Stripe `active`. **[видел]** Спишутся ли деньги автоматически — **[предположение]**, кнопка «Start subscription now» этого не уточняет.
- Цены без налогов; отмена в любой момент, доступ до конца оплаченного периода.

## 13. Источники данных и методология

- Meta: Meta Ad Library. В карточке у каждого объявления источник `fb_library` и ссылка на оригинал. **[видел]**
- EU reach и ранг — те же открытые сигналы Ad Library, что использует ads-monitor: охват по ЕС у объявлений с показом в ЕС и позиция в выдаче страницы.
- Google: Google Ads Transparency Center — рекламодатель-юрлицо, страны, диапазон показов в ЕЭЗ. **[видел]**
- В админ-маршрутах есть `collection/adheart`. **[предположение]** Часть истории могла быть загружена из стороннего сервиса Adheart.
- Свежесть: статус объявлений на момент проверки — 2026-10-10; охват «as of» 2026-10-12. Из 657 492 креативов с охватом за сутки обновлены 70 342, за неделю — 157 134. **[видел]** То есть охват обновляется выборочно, не у всех каждый день.
- Атрибуция бренда: объявление → домен посадочной → бренд и категория; воронка — из параметров URL (`branch-name=taichi`, `flow=4684`). **[предположение по структуре данных]** Каталог брендов ведётся вручную: в админке есть очередь кандидатов и вердикты.
- Дедупликация: три уровня — строгая группа (файлы, одинаковые на глаз), семейство по «телу» видео, семейство по «хуку».
- Расшифровка речи: Whisper `large-v3-turbo` с определением языка. **[видел]**
- Темы, подтемы, форматы и названия семейств размечены моделью (`label_source: "llm"`).
- Медиа лежат в Cloudflare R2, ссылки подписанные, живут 12 часов.

**Объёмы [видел]:**
- Meta, Analytics: 880 брендов, 2 261 домен, 8 063 страницы, 25 713 имён воронок, 28 категорий, 35 типов воронок, 103 языка; данные с 2025-01; сумма объявлений по брендам — 16,8 млн.
- Meta, Ads Library: 2 301 996 креативов (1 252 741 видео, 1 049 255 картинок), самый ранний — 2022-10-21.
- Google: 347 брендов, 518 доменов, 795 юрлиц, 233 страны.
- Категории по объёму: Fitness 4,6 млн; **Mental Health 1,69 млн (второе место)**; Nutrition & Diet 1,3 млн; Microlearning & Self-Growth 1,0 млн; Beauty & Skincare 0,93 млн.
- Типы воронок: Landing Page → App Subscription 7,1 млн; Quiz → App Subscription 3,5 млн; Landing Page → Other 1,2 млн; Free Trial → App Subscription 1,1 млн.

**Mental Health (наша ниша) [видел]:** 90 брендов. Топ по объёму: Liven 762 076, Hypnozio 142 353, Leaply 131 037, Chillio 75 447, BetterMe (ментальная линейка) 59 189, Copymind 57 874, Ultiself 51 243, Calm 40 457, AddMile 38 031, Breeze 28 266. Воронок в библиотеке — 55; у Liven, Chillio, Hypnozio, Selfony, BetterMe mind-quiz есть цены.

## 14. UX-проблемы и баги

- **Провал данных с августа 2026.** У Liven, Coursiv, Relatio, Keiki в сентябре и октябре — «—», MoM −100 %; у Headway 38 176 → 5 983 → 466. Столько крупных брендов не могли остановиться разом. **[предположение]** Это потеря покрытия, а не рынок; интерфейс этого не объясняет.
- **Абсурдные проценты.** Nebula «MoM +3763 %» (19 → 734) при «3M growth −69 %».
- **Шум в «New brands».** PlayStation, Air Arabia, Naver, Cardiff Music City — не web2app. Новые страницы без имён: «Page 828101270386826».
- **Имена воронок не уникальны.** `sp · 16cc5efe`, `jmpvG3GN`, `flow-2434` у разных доменов; сам скилл предупреждает, что строка по имени воронки — «a sum of strangers».
- **Поиск по тексту.** По «anxiety» при сортировке по умолчанию наверху PlantIn и Relatio — видимо, совпадение где-то в текстах, а порядок по числу объявлений.
- **ΔRank почти всегда «=».** В топ-60 таблицы все значения «=».
- **AI agent продаётся, но не работает** («coming soon»).
- **Противоречия про кредиты.** Лендинг и usage — 10 000 в месяц; SKILL.md — «50,000 data credits»; API отдаёт пакеты, а страница говорит, что купить их нельзя. В API поле `monthly_eur: 999`, на сайте — $999.
- **Следы внутренней кухни.** Названия кластеров экранов на русском («Оплата и тарифы», «Питание», «Без текстовой группы»), формат чисел `ru-RU`.
- **Даты в usage** в виде «12/09/2026 – 12/10/2026» — непонятно, день или месяц первым.
- **Пустая страница** `/dashboard/changelog`.
- **Тяжёлые ответы.** `/api/dashboard/filters` — 1,5 МБ на каждой странице, фасеты Ads Library — 1,27 МБ. Поиск по смыслу за 20 секунд в интерфейсе не завершился, хотя API ответил.
- **Нет экспорта** (CSV/PDF), **нет алертов и уведомлений**, **нет коллекций и избранного**. Saved views живут только в браузере. Поделиться можно только ссылкой на карточку.
- **Лимит двух сессий** на аккаунт.
- **Нет гео-фильтра по Meta**: страна есть только у Google. Про США отдельно ничего не узнать, охват — только ЕС.

## 15. Сильные стороны

- Готовая база рынка: не надо самому заводить конкурентов, 880 брендов и 28 категорий уже размечены.
- Воронки: скриншоты всех шагов, типы экранов, история версий с диффом, структурные цены, трекеры и платёжки, письма после регистрации.
- Concept families: группировка по хуку и телу, названия от модели, выживаемость 14 дней, «Potential winners».
- Поиск по смыслу (картинка и речь) и расшифровки видео.
- Разметка тем, подтем и форматов креативов.
- Честная методология: подсказки прямо говорят, чего метрика не значит.
- MCP со скиллом и прозрачным учётом кредитов.
- Google Ads как второй источник.
- Публичные отчёты, ниши и карточки брендов как бесплатная витрина.

## 16. Слабые места

- Заметные дыры в покрытии с августа 2026 у крупнейших брендов.
- Объём объявлений вместо spend; охват только по ЕС; ранг не сравним между страницами.
- Нет своих трекеров: нельзя добавить конкурента, которого нет в каталоге (на лендинге есть только форма «предложить URL»).
- Нет понятия «новое с прошлого визита», нет уведомлений — всё через ручные сортировки «за 7 / 14 дней».
- Воронки сняты в основном с немецкого рынка, цены в евро; для США данных мало.
- Нет экспорта и командной работы над подборками.
- Агент не запущен.
- Цена: $490 за модуль, $999 за всё.

## 17. Открытые вопросы

- **MCP вживую** не проверялся: для этого надо сгенерировать секретную ссылку, а это было запрещено условиями задачи. Формат ответов инструментов известен только по скиллу.
- **AI agent** — заглушка, проверять нечего.
- **Карточка concept family** в интерфейсе не открылась скриптом; прямой запрос `family-info` вернул 503, но параметры подбирались вручную, так что это не доказанный баг.
- **Saved views, настройка колонок, Metric filters** — элементы видны, сценарий до конца не проходился.
- **Профиль бренда для Google**, приглашение в команду, портал Stripe, смена тарифа — не открывались.
- **Скорость** не замерялась: страницы открывались за несколько секунд, точных цифр нет.
- **Частота обновления** Meta-данных и перепрохода воронок — выводы по датам в ответах, не по документации.
- **Что произошло с покрытием в августе–сентябре** — неизвестно.
- **Конверсия триала в оплату** — в интерфейсе не сказано.
- **Мобильную версию** не смотрели.
