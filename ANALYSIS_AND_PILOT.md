# Вариации чехлов 223 / 215 — анализ и пилот

## 1. Анализ файлов
- Источник истины по SKU↔ASIN: `All_Listings_Report_10-20-2026.xlsx` — 5612 SKU серий 223/215 (все Active; 5743 строк всего, остальные 131 — серии 406516/406517 и 1 служебный SKU, в задачу не входят).
- Feed Partial Update + ASIN покрывают **3451 SKU** (в `223/` и `LISTINGS-CASES-AMAZON/` лежат дубли версий: 4539 строк). Остальные **2161 SKU** (ряды 375–378, 415U, 445, 447U/448U, цвет F05) фидов Partial Update не имеют, но ASIN в отчёте есть.
- Маркетплейс US (ATVPDKIKX0DER), Product Type `CELLULAR_PHONE_CASE`, Template: заголовки в строке 5, данные с строки 7.
- Структура SKU: `<серия 223|215><код модели>-<код цвета>R?u001q<№ принта>`. Коды цвета 223: F02 Black, F06 Gray, F24 Beige, F05 Clear Matte, F16 Orange; 215: 001 Clear.
- **№ принта (qNNNNN) совпадает между сериями** (223 q124 «Football Runner» = 215 q124 «Football Run»), названия в листингах местами различаются — ключом надо считать номер принта. 187 принтов, в среднем 30 SKU на принт (макс. 60), у 163 принтов больше 20 SKU.
- 15 моделей iPhone: 13 (116), 13 Pro Max (118), 14 (195), 14 Pro Max (198), 15 (281), 15 Pro (283), 15 Pro Max (284), 16 (375), 16 Plus (376), 16 Pro (377), 16 Pro Max (378), 16e/17e (415U), 17 (445), 17 Pro/18 Pro (447U), 17 Pro Max/18 Pro Max (448U).

## 1а. Отчёт Amazon `US Category+Listings+Report_10-21-2026.xlsm` (Cases-ASIN-SKU-Inventory, main)
- Это актуальный шаблон Amazon (389 колонок, на 81 больше старого, старые все на месте) с живыми данными: 5743 SKU, все Active, у всех Listing Action = Edit (Partial Update). Строки 1–6 те же, данные с 7-й; перед SKU две справочные колонки (Status, Title).
- 5613 SKU `CELLULAR_PHONE_CASE` (5612 наших 223/215 + 1 чужой `3I-XT27-Q6AG`) и 130 SKU `PORTABLE_ELECTRONIC_DEVICE_COVER` (серии 406516/406517, не наши).
- **Вариаций сейчас нет ни у одного SKU** (Parentage, Parent SKU, Variation Theme пустые). Поля Style, Theme, Model Name, Case Type тоже пусты.
- Живые значения совпадают с фидами: Color = Clear 1959 (F05 и серия 215 оба «Clear», отличаются Finish Type: Matte / Smooth), Beige 1485, Black 1132, Gray 915, Orange 122; модели iPhone 13…17.
- Картинки уже на Amazon (`m.media-amazon.com`) — для parent использую их.
- `Dangerous Goods Regulations`: поле «Required» в Data Definitions относится только к `PORTABLE_ELECTRONIC_DEVICE_COVER` (его выпадающий список и правило обязательности привязаны только к этому типу); для `CELLULAR_PHONE_CASE` списка и правила нет, поле остаётся пустым.

## 2. Тема вариации
`COLOR/COMPATIBLE_PHONE_MODELS` есть в списке допустимых тем шаблона. По решению: в `COLOR` пишется **название принта**, в `COMPATIBLE_PHONE_MODELS` — модель iPhone. Parent = Create or Replace (Full Update), Child = Edit (Partial Update), ≤ 20 SKU в семействе.

Риск: список допустимых значений Color в шаблоне — цвета (Black, Gray, …). Пилот проверяет, примет ли Amazon название принта.

## 3. Схема и тест `VAR_TEST_Parent-1.xlsm`
**Схема (утверждена):** Parent = принт × поколение iPhone (13/14/15/16/17), внутри смешиваются все типы чехлов и обе серии (935 семейств, максимум 16 child, лимит 20 соблюдён). Тип чехла кодируется в Color из кода SKU, чтобы пара (Color, модель) была уникальной:
`{Принт} - {тип}`; типы: F02 Black Smoky Matte, F06 Gray Frosted Matte, F24 Beige Matte, F05 Clear Matte, F16 Orange, 215/001 Clear. Для одной пары «принт + модель» в Amazon бывает 2–5 типов (1806 пар), без этого child были бы дубликатами.

**Тест:** Parent-1 (Create or Replace, Full Update) + 10 child (Edit, Partial Update): принт Little Lamb (q86) × iPhone 13 / 13 Pro Max × 5 типов (223 F02/F06/F24/F05 + 215 Clear). Состав и ASIN — в `VAR_TEST_Parent-1_mapping.csv`.
- Child-строки: SKU, Product Type, действие, Parentage Level, Parent SKU, Variation Theme, Brand, Color, Compatible Phone Models. Без имени и ASIN. Item Name в шаблоне помечен Required — если Amazon потребует, добавим.
- Parent Title (74): `MOBILIUS MagSafe Compatible Case for iPhone 13 and 13 Pro Max, Little Lamb`
- Item Highlight (103): `Black Smoky Matte, Gray Frosted Matte, Beige Matte, Clear Matte, Clear, 2.12 mm TPU back, camera cutout`
- 3 Bullets: принт/модели/типы чехлов; MagSafe-кольцо; TPU-задняя панель 2.12 мм с вырезом камеры.
- Description (788): модели, принт, типы, MagSafe, материал, назначение. Тексты Parent содержат только то, что верно для всех типов 223 и 215 (материал только TPU; поликарбонат, Finish и цвет не указаны).
- Проверки: Title ≤ 75 (74), слово не более 2 раз, Highlight ≤ 125, буллеты 11–254, Description ≤ 2000; пары (Color, модель) уникальны; модели совпадают с отчётом Amazon; изменён только лист Template, строки 1–6 и остальные листы не тронуты. В Amazon не загружался.
- `Dangerous Goods Regulations` относится только к `PORTABLE_ELECTRONIC_DEVICE_COVER`; для `CELLULAR_PHONE_CASE` не нужно.
- Риск: Color содержит принт и тип, а не чистый цвет — проверить тестовой загрузкой.


## 5. Результат загрузки Parent-1 (Amazon, 2026-10-21)
11 из 11 SKU SUCCESS, ошибок 0. Два предупреждения у child Gray Frosted Matte — рекомендация перейти на FBA, к вариациям не относится.

## 6. Тест на максимум и пачки на все семейства (обновлено 2026-10-22)
- `VAR_TEST_Parent-2_max16.xlsm` — Parent-2 + 16 child: принт Tokyo Dragon × iPhone 16 / 16 Plus / 16 Pro / 16 Pro Max / 16e.
- `batches/VAR_BATCH_01…07` — Parent-3 … Parent-935, все 934 семейства кроме Parent-1; 5602 child вместе с Parent-2 (5612 − 10 в Parent-1). Файлы по ~1000 строк, семейство не делится между файлами. Загружать после успешного теста Parent-2.
- Семейство = принт × поколение iPhone, Color = `{Принт} - {тип чехла}`. Названия принтов берутся из `215 SKU, принты, описания.xlsx` (подтверждены владельцем), для принтов, которых там нет, — из заголовков 223.
- Parent: тексты по правилам Parent-1 (Title ≤ 74, Highlight ≤ 125, 3 буллета, Description ≤ 2000) + **Style = Theme = название принта**.
- Принты с риском HIGH/MEDIUM из `215 Amazon policy check.xlsx` включены; риск помечен в `VAR_ALL_mapping.csv` (колонка policy_risk): 40 семейств HIGH, 64 MEDIUM.
- `VAR_one_child_families.csv` — 23 семейства из одного child.
- Проверка по всем файлам: SKU не повторяются, каждый child ссылается на Parent из того же файла, ≤ 20 child в семействе, пары (Color, модель) уникальны, модели совпадают с отчётом Amazon, лимиты текстов, Style/Theme заполнены, строки 1–6 и остальные листы шаблона не менялись; покрытие 5602 из 5602.
