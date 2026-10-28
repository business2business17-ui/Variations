# Variations — MOBILIUS MagSafe cases (серии 223 и 215)

Эталон: живые семейства Parent-2 (B0HP42GQKQ, iPhone 16 Series, Tokyo Dragon) и Parent-1 (B0HNT5PGXT, iPhone 13 и 13 Pro Max, Little Lamb). Тема `COLOR/COMPATIBLE_PHONE_MODELS`.

## Схема (как Parent-1/Parent-2)
- **1 Parent = 1 принт × 1 поколение iPhone** (13 / 14 / 15 / 16 / 17). Внутри — все типы чехлов обеих серий и все модели поколения.
- Child: `Color` = `{принт} - {тип чехла}`, `Compatible Phone Models` = модель; пары (Color, модель) уникальны. Child ≤ 16 на Parent.
- Parent: `Parent-N` (N = 3…935), Create or Replace (Full Update); child: Edit (Partial Update). Заголовок `MOBILIUS MagSafe [Compatible] Case for iPhone … , {принт}`.
- Почему так: схемы «много принтов в одном Parent» (`*-Parent-1`, `223/215-Parent-2`) Amazon создавал обычными листингами, а не Parent; оба рабочих эталона содержат один принт. Эти файлы — в `_failed_schemes/`.

## Проверено скриптом
933 Parent, 5586 child; на каждый Parent один принт; child ≤ 20; пары (Color, модель) уникальны; заголовки уникальны и ≤ 75; набор child = все 5612 SKU серий 223/215 минус 26 child живых Parent-1/2.
23 семейства с одним child — `VAR_one_child_families.csv`.

## Порядок загрузки
1. `VAR_DELETE_stray_215-223_Parent-1_Parent-2.xlsm` — удалить 4 ошибочных листинга (`223-Parent-1`, `215-Parent-1`, `223-Parent-2`, `215-Parent-2`). Только по команде владельца.
2. `VAR_TEST_print_5_parents.xlsm` — тест: 5 Parent (по одному на поколение, Parent-3/189/376/563/749), 30 child. Проверить, что семейства собрались (у Parent открывается «View variations»).
3. `batches_by_print/VAR_BATCH_01…07` — по очереди, после успешного теста.

Файлы-справочники: `VAR_ALL_mapping.csv`, `VAR_parents_overview.csv`, `VAR_parents_live.csv`, `ANALYSIS_AND_PILOT.md`, эталоны `VAR_TEST_Parent-1.xlsm`, `VAR_TEST_Parent-2_max16.xlsm`.

## Вариант «Parent = модель, внутри принты» (запрос владельца)
Сценарий покупателя: листинг своей модели → внутри выбор принта. Тема `COLOR` (в шаблоне разрешена для CELLULAR_PHONE_CASE), Color = название принта, модель — обычный атрибут. Прежние попытки с одной моделью использовали `COLOR/COMPATIBLE_PHONE_MODELS`, где у измерения «модель» одно значение; тема только `COLOR` ещё не загружалась.
Тест: `VAR_TEST_model_COLOR_iPhone13.xlsm` — 2 Parent (`215-M13-Clear`, 19 child; `223-M13-F02`, 18 child), iPhone 13. Если соберутся — остальные модели строятся так же.
