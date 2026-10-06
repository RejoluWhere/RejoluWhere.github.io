# Spark SQL: готовые запросы с группировкой

> **Учебная работа.** Таблица `dealer` и основа запросов взяты из справочника по SQL проекта Apache Spark. Тексты и пояснения мои. В официальную документацию проекта работа не входит.

**Для кого:** для аналитика, который знает основы SQL и хочет быстро получить ответы на типовые вопросы о данных.

**Как пользоваться:** найдите нужный вопрос, скопируйте запрос и выполните его. Запросы после раздела «Подготовка данных» только читают данные.

**Где выполнять:** в консоли `./bin/spark-sql` или через `spark.sql("...")` в PySpark.

## Подготовка данных

Создайте учебную таблицу и заполните её:

```sql
CREATE TABLE dealer (id INT, city STRING, car_model STRING, quantity INT);

INSERT INTO dealer VALUES
  (100, 'Moscow',  'Honda Civic',  10),
  (100, 'Moscow',  'Honda Accord', 15),
  (100, 'Moscow',  'Honda CRV',     7),
  (200, 'Omsk',   'Honda Civic',  20),
  (200, 'Omsk',   'Honda Accord', 10),
  (200, 'Omsk',   'Honda CRV',     3),
  (300, 'ChinaTown', 'Honda Civic',   5),
  (300, 'ChinaTown', 'Honda Accord',  8);
```

| Поле | Тип | Описание |
|------|-----|----------|
| `id` | INT | Идентификатор дилера |
| `city` | STRING | Город дилера |
| `car_model` | STRING | Модель автомобиля |
| `quantity` | INT | Количество автомобилей этой модели |

## Сколько автомобилей у каждого дилера?

```sql
SELECT id, sum(quantity) AS total
FROM dealer
GROUP BY id
ORDER BY id;
```

| id | total |
|----|-------|
| 100 | 32 |
| 200 | 33 |
| 300 | 13 |

## В каких городах больше 15 автомобилей?

Условие `HAVING` проверяется после группировки, поэтому в нём можно использовать `sum`. В `WHERE` так сделать нельзя.

```sql
SELECT city, sum(quantity) AS total
FROM dealer
GROUP BY city
HAVING sum(quantity) > 15
ORDER BY city;
```

| city | total |
|------|-------|
| Omsk | 33 |
| Moscow | 32 |

## Какой модели больше всего по суммарному количеству?

Запрос суммирует поле `quantity`. Если заменить `sum(quantity)` на `count(*)`, получится число строк в таблице, а не число автомобилей.

```sql
SELECT car_model, sum(quantity) AS total
FROM dealer
GROUP BY car_model
ORDER BY total DESC;
```

| car_model | total |
|-----------|-------|
| Honda Civic | 35 |
| Honda Accord | 33 |
| Honda CRV | 10 |

## Сколько разных моделей в каждом городе?

```sql
SELECT city, count(DISTINCT car_model) AS models
FROM dealer
GROUP BY city
ORDER BY city;
```

| city | models |
|------|--------|
| Omsk | 3 |
| Moscow | 3 |
| ChinaTown | 2 |

## Если запрос не работает

| Что произошло | Причина | Что делать |
|---------------|---------|------------|
| Ошибка анализа: столбец не входит в `GROUP BY` и не агрегирован | В `SELECT` есть столбец, по которому нет группировки | Добавьте столбец в `GROUP BY` или оберните в агрегатную функцию |
| Результат пустой | Условие `HAVING` слишком строгое | Уменьшите порог или уберите `HAVING` |

## Где узнать больше

- [GROUP BY Clause](https://spark.apache.org/docs/latest/sql-ref-syntax-qry-select-groupby.html) — оригинальная страница справочника.
