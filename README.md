# Olist E-commerce Analytics (Pet Project)

Учебный data analytics проект на датасете **Olist Brazilian E-Commerce**: сбор витрины заказов и аналитический дашборд KPI.

## https://datalens.yandex/sg3mtguoaohgc

## 1) Цель проекта
Собрать понятную витрину уровня “1 заказ = 1 строка” и построить дашборд для ответов на базовые продуктовые вопросы:
- как менялись заказы и GMV со временем
- какой средний чек (AOV) и как он меняется
- насколько надежна доставка (доля доставленных, распределение задержек)
- как задержка доставки влияет на отзывы клиентов

## 2) Данные
Источник: Olist Brazilian E-Commerce (9 CSV таблиц).

Используемые таблицы:
- `olist_orders_dataset.csv` — заказы и даты статусов
- `olist_order_items_dataset.csv` — состав заказа (цена, доставка)
- `olist_order_payments_dataset.csv` — платежи и тип оплаты
- `olist_order_reviews_dataset.csv` — оценки и отзывы
- `olist_customers_dataset.csv` — клиент и локация
- `olist_products_dataset.csv` + `product_category_name_translation.csv` — категория товара

Файлы лежат в `data/raw/`.

## 3) Инструменты
- Python: `pandas`, `numpy`, `matplotlib`
- Визуализация: **Yandex DataLens**
- Формат витрины: CSV (для загрузки в DataLens)

## 4) Структура проекта
Пример структуры (можно адаптировать под свой репозиторий):

data/
raw/ # исходные CSV Olist
processed/ # витрина и агрегаты для DataLens
notebooks/
  01_eda.ipynb # первичная проверка данных
  02_mart_build.ipynb # сбор витрины mart_orders.csv
  03_insights.ipynb # графики/проверки/агрегаты
  assets/ # скриншоты дашборда
  README.md

## 5) Витрина данных (mart)
Главная витрина: `data/processed/mart_orders.csv`

Гранулярность: **1 строка = 1 заказ (`order_id`)**

Ключевые поля витрины (пример):
- идентификаторы: `order_id`, `customer_id`, `customer_unique_id`
- география: `customer_city`, `customer_state`
- статусы и даты: `order_status`, `order_purchase_timestamp`, `order_delivered_customer_date`, `order_estimated_delivery_date`
- финансы: `gmv_items`, `freight_total`, `gmv_total`, `payment_value`
- платежи: `payment_type_main`, `installments_max`
- отзывы: `review_score`
- операционные метрики:
  - `delivery_days` — длительность доставки
  - `delay_days` — задержка относительно ожидаемой даты (0 = вовремя)
  - `delivered_flag_fix` — 0/1 (доставлен)
  - `on_time_flag_fix` — 0/1 (доставлен вовремя)
  - `bad_review_flag_fix` — 0/1 (оценка <= 2)
- временная агрегация: `purchase_month`

Дополнительно для графика влияния задержек на отзывы использован агрегат:
- `data/processed/delay_vs_review.csv` (бакеты задержек -> средняя оценка)

## 6) Дашборд (DataLens)
Дашборд содержит:
- **KPI карточки**: Заказы, GMV, Доля доставленных, Средняя оценка
- **Тренды по месяцам**: Заказы, GMV, AOV
- **Доставка**: распределение задержек (delay bucket)
- **Отзывы vs задержка**: средняя оценка по бакетам задержки

Скриншоты дашборда лежат в `assets/`:
- `assets/dashboard.png`

## 7) Основные выводы (insights)
1) **Масштаб:** ~99k заказов, GMV ~15.8M BRL, средняя оценка ~4.09.
2) **Динамика:** с 2016 по 2018 наблюдается рост заказов и GMV, пик активности — конец 2017 / начало 2018.
3) **Доставка:** доля доставленных заказов около 97%, большинство доставок близко к ожидаемой дате.
4) **Опыт клиента:** при задержке доставки более ~5 дней средняя оценка заметно падает.
