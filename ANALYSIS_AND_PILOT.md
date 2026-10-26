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

## 6. Схема по сериям (с 2026-10-26, заменяет схему «принт × поколение»)
- Серии раздельно: `223-Parent-N` и `215-Parent-N`, нумерация по каждой серии с 1.
- 1 Parent = 1 модель iPhone, внутри разные принты. Для 223 дополнительно 1 Parent = 1 тип чехла (Black Smoky Matte, Gray Frosted Matte, Beige Matte, Clear Matte, Orange): тексты и материал у типов разные. Принты идут по номеру, при > 20 принтов группа делится на равные части (≤ 20 child).
- 314 Parent: 215 — 88, 223 — 226; 5612 child (всё, включая 26 SKU из Parent-1/Parent-2: они переезжают в новые Parent).
- Child: Color = название принта (файл 215, иначе заголовки 223), Compatible Phone Models = модель (одна на Parent), Parent SKU, бренд, Edit (Partial Update).
- Parent: Title = `MOBILIUS {тип чехла} Case for {модель}, {принты, сколько влезает в 74 символа}`; у 6 Parent (Gray Frosted Matte на Pro Max/Plus) принт в Title не помещается — `Printed Designs`. Highlight — факты о типе чехла. Буллет 1 — принты. Буллеты 2–3 и Description из живых листингов этого типа; в Description все принты. Style = Theme = `Printed Designs` (принтов в Parent много).
- Файлы: `VAR_TEST_series_215-Parent-1_223-Parent-1.xlsm` (по одному Parent каждой серии, 39 строк), `batches_by_series/VAR_215_*` и `VAR_223_*` (по ~1000 строк, семейство целиком в одном файле). `VAR_ALL_mapping.csv`, `VAR_parents_overview.csv`.
- Parent-1 и Parent-2 (старая схема) остаются на Amazon пустыми после переноса child; удаление — только после вашего подтверждения.

## 7. Решения владельца (2026-10-26)
- **Parent-1 (B0HNT5PGXT) и Parent-2 (B0HP42GQKQ) со старой схемой остаются на Amazon пока что** — не удалять, не менять без команды владельца. Их 26 child в новых файлах привязаны к новым Parent (`223-Parent-N` / `215-Parent-N`); пока новые файлы не загружены, ничего не меняется.
- **Тема вариации должна исключать дубли.** При Color = «принт» 4869 из 5612 child имели такую же пару (Color, модель), как child в других Parent (до 5 повторов) — Amazon мог принять их за дубли. Теперь Color = `{принт} - {тип чехла}` (как у Parent-1/2); все 5612 пар (Color, модель) уникальны во всём каталоге, а не только внутри Parent.
