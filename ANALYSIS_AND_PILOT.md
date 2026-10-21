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

## 3. Тест `VAR_TEST_Parent-1.xlsm` (шаблон из отчёта Amazon)
9 строк: **Parent-1** (Create or Replace, Full Update) + 8 child (Edit, Partial Update). Состав и ASIN — в `VAR_TEST_Parent-1_mapping.csv`.
- Связка: Variation Theme `COLOR/COMPATIBLE_PHONE_MODELS`; у child Color = название принта (Samurai Calligraphy, Vintage Racing), Compatible Phone Models = iPhone 13 / 13 Pro Max / 14 / 14 Pro Max.
- Parent Title (69 симв.): `MOBILIUS Black Smoky Matte Case for iPhone 13 and 14, Printed Designs`
- Item Highlight (107): `Samurai Calligraphy, Vintage Racing, 13 Pro Max, 14 Pro Max, magnetic ring compatible with MagSafe chargers`
- Bullets (3): 1) `Prints and models: Choose Samurai Calligraphy or Vintage Racing for iPhone 13, iPhone 13 Pro Max, iPhone 14 or iPhone 14 Pro Max`; 2) MagSafe compatible magnetic ring (N45, 2300 Gs, 55/46 mm); 3) Smoky matte back (2.12 mm TPU + polycarbonate, camera cutout)
- Description (988): принты и модели, MagSafe, матовый корпус, материал, назначение — только факты из живых листингов.
- Требования (rules.json, 2026-10-06): Title ≤ 75 (Item Highlights работают только при этом), слово не более 2 раз; Highlight ≤ 125, фразы через запятую; Bullet 11–254, с заглавной, с заголовком, без точки; Description ≤ 2000, без HTML. Скрипт сборки проверяет эти лимиты.
- Parent без цены, остатка, Color и модели. Меняется только лист Template, строки 1–6 и остальные листы не тронуты. В Amazon не загружался.

## 4. Открытые решения (нужны до масштабирования)
1. Что объединяет семейство (parent): в пилоте — серия+цвет чехла+поколение, дети = принт × модель. Пока не подтверждено.
2. Смешивание серий 223+215: при COLOR=принт пара (принт, модель) у 223 и 215 совпадёт → дубликат темы. Нужно решить, чем различать (например отдельные parent по серии, либо другая тема).
3. Parent SKU-нейминг и главное изображение parent.
4. Child-строки Partial Update: оставить имя/бренд/ASIN или только SKU+связь.
