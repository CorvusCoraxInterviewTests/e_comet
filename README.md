Задание: 
SQL запрос, который удалит из таблицы cities=(date, city_name, temperature) с PK=(date, city_name) два последних 
измерения температуры для каждого города.

Создание таблицы.
```
CREATE TABLE cities (
	date DATE, 
	city_name VARCHAR(15), 
	temperature DECIMAL(4, 2),
	PRIMARY KEY (date, city_name)
);
```
Заполнение таблицы тестовыми данными.
```
INSERT INTO cities
VALUES 
	(TO_DATE('2023-07-15', 'YYYY-MM-DD'), 'Москва', 22.50),
	(TO_DATE('2023-07-15', 'YYYY-MM-DD'), 'Берлин', 22.50),
	(TO_DATE('2023-07-15', 'YYYY-MM-DD'), 'Токио', 22.50),
	(TO_DATE('2023-07-14', 'YYYY-MM-DD'), 'Москва', 21.50),
	(TO_DATE('2023-07-14', 'YYYY-MM-DD'), 'Берлин', 21.50),
	(TO_DATE('2023-07-14', 'YYYY-MM-DD'), 'Токио', 21.50),
	(TO_DATE('2023-07-13', 'YYYY-MM-DD'), 'Москва', 20.50),
	(TO_DATE('2023-07-13', 'YYYY-MM-DD'), 'Берлин', 20.50),
	(TO_DATE('2023-07-13', 'YYYY-MM-DD'), 'Токио', 20.50),
	(TO_DATE('2023-07-12', 'YYYY-MM-DD'), 'Москва', 19.50),
	(TO_DATE('2023-07-12', 'YYYY-MM-DD'), 'Берлин', 19.50),
	(TO_DATE('2023-07-12', 'YYYY-MM-DD'), 'Токио', 19.50),
	(TO_DATE('2023-07-11', 'YYYY-MM-DD'), 'Москва', 18.50),
	(TO_DATE('2023-07-11', 'YYYY-MM-DD'), 'Берлин', 18.50),
	(TO_DATE('2023-07-11', 'YYYY-MM-DD'), 'Токио', 18.50)
;
```
Запрос, удаляющий показания за последния два дня во всех городах.
```
DELETE
FROM cities
WHERE (date, city_name) >= ANY (
	SELECT DISTINCT NTH_VALUE(date, 2) OVER w, city_name
		FROM cities
		WINDOW w AS (
			PARTITION BY city_name 
			ORDER BY date DESC
			ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
		)
	)
;
```
