## ClickHouse: на ходу отобрать колонки по условию

**Описание задачи**:

Мы пишем длинный запрос, в котором соединяем таблицы, и в конце мы хотим
получить "чистый" результат без некоторых ненужных колонок.

Ненужные колонки будут называться так: t2.<название_колонки>

Как их нам отобрать автоматически, не прописывая вариант наподобие
SELECT *EXCEPT( названия колонок через запятые) ? 
Этот вариант не годится, т.к. названия колонок могут быть разными.

----------------------------------------------------------------
Мы можем использовать **SELECT COLUMNS('regexp')** - куда внутрь можем подставить регулярное выражение, которое отберёт только нужные колонки.

https://clickhouse.com/docs/ru/sql-reference/statements/select

Нужные колонки - все, какие угодно, кроме t2.<название_колонки>

Проверять регулярные выражения можно здесь: https://regex101.com/

Но главное, чтобы запрос работал в ClickHouse.

----------------------------------------------------------------
**Тестовые данные:**

    WITH t1 AS (
    SELECT 
    1 AS `column_name`, 
    1 AS `t1.column_name`, 
    1 AS `t2.column_name`, 
    1 AS `t2.some_column_name`, 
    1 AS `abcd`, 
    1 AS `table_column_name`,
    1 AS `__datetime`,
    1 AS `_table_name`)


    SELECT COLUMNS('') FROM t1 

Что нужно подставить внутрь COLUMNS('') чтобы отобрать все колонки, кроме t2.<название_колонки> из этого примера?

----------------------------------------------------------------

**Моё решение**: [здесь](https://github.com/Malakhova-Natalya/Snippets/blob/main/clickhouse/clickhouse_columns_regexp/01%20-%20описание%20задачи%20COLUMNS(regexp).txt)

----------------------------------------------------------------

**Описание задачи**: взять любые названия колонок кроме тех, где в любом месте есть точка


**Тестовые данные:**

        WITH t1 AS (
        SELECT 
        1 AS `.column_name`,
        1 AS `column_name`, 
        1 AS `t1.column_name`, 
        1 AS `t2.column_name`, 
        1 AS `t2.some_column_name`, 
        1 AS `abcd`, 
        1 AS `table_column_name`,
        1 AS `__datetime`,
        1 AS `_table_name`,
        1 AS `_table_name2`)

        SELECT COLUMNS('') FROM t1 

**Моё решение**:

        WITH t1 AS (
        SELECT 
        1 AS `.column_name`,
        1 AS `column_name`, 
        1 AS `t1.column_name`, 
        1 AS `t2.column_name`, 
        1 AS `t2.some_column_name`, 
        1 AS `abcd`, 
        1 AS `table_column_name`,
        1 AS `__datetime`,
        1 AS `_table_name`,
        1 AS `_table_name2`)

        SELECT COLUMNS('^[a-zA-z|_|0-9]*$') FROM t1 

        COLUMNS('^[^.]+$')
