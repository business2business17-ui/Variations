# Variations — MOBILIUS MagSafe cases (серии 223 и 215)

## Рабочая схема (подтверждена загрузкой теста 2026-10-29)
Parent = **модель iPhone × серия × тип чехла** (в 223 — по типу: Black Smoky Matte, Gray Frosted Matte, Beige Matte, Clear Matte, Orange; в 215 — Clear). Внутри все принты этой модели.
- Тема вариации `COLOR`; `Color` = название принта; модель — обычный атрибут child. Тест `VAR_TEST_model_COLOR_iPhone13.xlsm` создал оба семейства: `223-M13-F02` (18), `215-M13-Clear` (19).
- Не работали: темы `COLOR/COMPATIBLE_PHONE_MODELS` при одной модели на Parent и при многих принтах в Parent (Amazon создавал обычные листинги) — файлы в `_failed_schemes/`.
- Parent SKU: `{серия}-M{модель}-{тип}`, напр. `223-M16PM-F06`. Parent = Create or Replace (Full Update), child = Edit (Partial Update).
- 60 Parent (215 — 15, 223 — 45), 5586 child, в семействе до 149 принтов. Живые Parent-1 (B0HNT5PGXT) и Parent-2 (B0HP42GQKQ) остаются, их 26 child в новые файлы не входят.

## Порядок загрузки
1. `VAR_DELETE_stray_215-223_Parent-1_Parent-2.xlsm` — удалить 4 ошибочных листинга (`223-Parent-1`, `215-Parent-1`, `223-Parent-2`, `215-Parent-2`).
2. `batches_by_model/VAR_MODEL_01…06` по очереди (≈ 800–1000 строк каждый; семейство целиком в одном файле). Файл 01 содержит и два тестовых семейства — они обновятся и получат все принты. После каждого файла проверять «View variations».
3. Если большие семейства (149 child) не соберутся — запасной вариант: `batches_by_print/` (Parent = принт × поколение).

Справочники: `VAR_ALL_mapping.csv` (parent, модель, тип, child SKU, принт), `VAR_parents_overview.csv`, `VAR_parents_live.csv`, `VAR_one_child_families.csv` (относится к запасной схеме), `ANALYSIS_AND_PILOT.md`. Эталоны: `VAR_TEST_Parent-1.xlsm`, `VAR_TEST_Parent-2_max16.xlsm`.
