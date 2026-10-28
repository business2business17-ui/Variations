# Variations — MOBILIUS MagSafe cases (серии 223 и 215)

Тема вариации: `COLOR/COMPATIBLE_PHONE_MODELS`, как у живых Parent-1 (B0HNT5PGXT) и Parent-2 (B0HP42GQKQ).

## Схема (проверена скриптом на всех файлах)
- Parent: `223-Parent-N` / `215-Parent-N` (N с 2; `*-Parent-1` заняты тестовыми листингами). Серия × тип чехла (для 223) × линейка моделей iPhone; в каждом Parent ≥ 2 модели, принты делятся на части, child ≤ 20.
- Child: `Color` = `{принт} - {тип чехла}`, `Compatible Phone Models` = модель iPhone; пара (Color, модель) уникальна во всём каталоге (0 повторов).
- Parent = Create or Replace (Full Update), child = Edit (Partial Update). Style/Theme в Parent не заполняются (как у Parent-1/2).
- Живые Parent-1 и Parent-2 остаются на Amazon, их 26 child в новые файлы не входят.

## Итог проверки
300 Parent, 5586 child; все child-SKU уникальны и совпадают с `VAR_ALL_mapping.csv`; заголовки Parent уникальны и ≤ 75 символов. Модели в child совпадают с таблицами 215/223 (в «16e / 17e», «17 Pro / 18 Pro» и т.п. записана модель из листинга Amazon).

## Файлы
- `VAR_TEST_multi_215-Parent-2_223-Parent-2.xlsm` — тест (2 Parent, 40 child): загружать первым.
- `batches_multi/` — 7 файлов (215: 2, 223: 5), загружать по порядку после успешного теста.
- `VAR_DELETE_223-Parent-1_215-Parent-1.xlsm` — удаление двух ошибочных листингов (только по команде владельца).
- `VAR_ALL_mapping.csv`, `VAR_parents_overview.csv`, `VAR_parents_live.csv` — соответствия; `ANALYSIS_AND_PILOT.md` — история решений.
- Старые тесты (`VAR_TEST_Parent-1/2`, `VAR_TEST_series_*`) — справочные эталоны.
