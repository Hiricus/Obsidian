## Установить значение поля по условию
```sql
SELECT title, price,
CASE
	WHEN price > 700 THEN 'high'
	WHEN price BETWEEN 600 AND 700 THEN 'medium'
	ELSE 'low'
END AS price_desc
FROM book;
```
В блоке `CASE` задаются условия и результаты при выполнении текущего условия. Все результаты должны быть одного типа данных, иначе запрос не сработает.

## Выборка данных по условию
```sql
SELECT title, author, price
FROM book
WHERE price > 600;
```
После слова `WHERE` задаётся условие, при выполнении которого для текущей строки она будет представлена в ответе на запрос.

## Сортировка по значению определённого поля
```sql
SELECT title, author, price
FROM book
ORDER BY price DESC;
```
В данном примере сортировка производится по убыванию цены.
**Для сортировки можно выбрать поле, которого нет в запросе**.

## Задача на фильтрацию с подзапросом
Узнать сколько авторов, у которых есть книги со стоимостью более 500 и количеством более 1 шт. на складе, при количестве различных названий произведений не менее 2-х. Вывести подходящих авторов.
```sql
SELECT author
FROM book
WHERE author IN (
	SELECT author
	FROM book
	WHERE price > 500 AND amount > 1
)
GROUP BY author
HAVING (COUNT(DISTINCT title) >= 2);
```

## Группировка по цене
Сгруппировать книги по ценовому диапазону в 4 группы. Для каждой группы посчитать общее число книг на складе.
```sql
SELECT
CASE
	WHEN price < 500 THEN 'Books under 500'
	WHEN price BETWEEN 500 AND 599.99 THEN 'Books from 500 to 599'
	WHEN price BETWEEN 600 AND 699.99 THEN 'Books from 600 to 699'
	ELSE 'Books above 700'
END AS price_range,
SUM(amount) AS books_total
FROM book
GROUP BY price_range
ORDER BY price_range;
```

## Выбор всех книг дороже средней цены
```sql
SELECT author, title, price
FROM book
WHERE price > (SELECT AVG(price) FROM book);
```

## Выбор всех книг, число которых отличается от среднего
В данном примере выбираются все книги, число которых на складе больше/меньше среднего на более чем 3. Чтобы учесть ситуацию и с большим и с меньшим числом используем функцию модуля `ABS`.
Для получения среднего используем подзапрос.
```sql
SELECT author, title, amount
FROM book
WHERE ABS(amount - (SELECT AVG(amount) FROM book)) > 3;
```

## Отбор книг по коллекции авторов
Вывести информацию о книгах тех авторов, общее количество экземпляров книг которых не менее 12
```sql
SELECT title, author, amount, price
FROM book
WHERE author IN (
	SELECT author
	FROM book
	GROUP By author
	HAVING SUM(amount) >= 12
);
```
Вложенный запрос отбирает нужных авторов, после чего в основном запросе для каждой записи проверяется то, входит ли автор в список, полученный из подзапроса.

## Выбор книг, для которых количество не дублируется
Вывести информацию (автора, книгу и количество) о тех книгах, количество экземпляров которых в таблице `book` не дублируется.
В подзапросе производится группировка по полю `amount`, после чего находится число записей в каждой группе. Если число записей 1 - следовательно значение уникально.
```sql
SELECT author, title, amount
FROM book
WHERE amount IN (SELECT amount
	FROM book
	GROUP BY amount
	HAVING COUNT(amount) = 1);
```

## Сравнение с коллекцией при помощи `ANY`
Вывести информацию о книгах, цена которых меньше самой большой из минимальных цен, вычисленных для каждого автора.
```sql
SELECT author, title, price
FROM book
WHERE price < ANY ( SELECT MIN(price)
	FROM book
	GROUP BY author);
```

