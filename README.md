# Infectious Diseases Data Analysis — PostgreSQL

Навчальний проєкт із реляційних баз даних: імпорт CSV, нормалізація до третьої нормальної форми (3NF), агрегування даних, робота з датами та створення користувацьких SQL-функцій у PostgreSQL.

## Технології

- **PostgreSQL** — зберігання й аналіз даних;
- **Python 3.12** та **pandas** — читання CSV;
- **SQLAlchemy**, **psycopg** та **pgserver** — підключення до локального PostgreSQL;
- **Jupyter Notebook** (`.ipynb`) та **ipython-sql** (`%%sql`) — виконання SQL у VS Code.

## Дані

Вхідний файл: `infectious_cases.csv`.

Набір містить **10 521 рядок і 12 колонок** зі статистикою інфекційних захворювань за роками та географічними одиницями. Колонки: `Entity`, `Code`, `Year`, `Number_yaws`, `polio_cases`, `cases_guinea_worm`, `Number_rabies`, `Number_malaria`, `Number_hiv`, `Number_tuberculosis`, `Number_smallpox`, `Number_cholera_cases`.

Дані містять пропущені значення, а `Entity` охоплює не лише країни, а й регіони та агреговані групи (наприклад, `World` і `G20`). Пропуски не замінюються нулями автоматично.



## Запуск

1. Відкрити папку проєкту у VS Code.
2. Створити й активувати віртуальне середовище:

   ```bash
   python3.12 -m venv .venv
   source .venv/bin/activate
   ```

3. Встановити залежності:

   ```bash
   python -m pip install -r requirements.txt
   ```

   Мінімальний перелік залежностей: `pandas`, `SQLAlchemy`, `pgserver`, `psycopg[binary]`, `ipython-sql`, `ipykernel`.

4. Розмістити `infectious_cases.csv` у папці проєкту.
5. Відкрити `fp.ipynb` та обрати Python-ядро з `.venv`.
6. Запустити PostgreSQL і налаштувати підключення у Python-комірці:

   ```python
   import pgserver
   from sqlalchemy import create_engine

   pg = pgserver.get_server('/tmp/fp_pg', cleanup_mode='stop')
   db_url = pg.get_uri()
   engine = create_engine(db_url, future=True)
   ```

7. Підключити SQL Magic у наступній Python-комірці:

   ```python
   %load_ext sql
   %sql $db_url
   ```

8. Виконувати комірки notebook **послідовно**, починаючи зі створення схеми та імпорту даних. Після перезапуску ядра потрібно повторно виконати комірки підключення.

> **Примітка:** `SET search_path` діє для конкретної сесії PostgreSQL. У запитах використовується явна назва схеми `pandemic`, а під час імпорту pandas — `schema="pandemic"`.


## Примітки

- Повторний запуск `to_sql(..., if_exists="replace")` перестворює вихідну таблицю.
- Повторний запуск `INSERT` до нормалізованої таблиці без очищення може створити дублікати.
- Для SQL-запитів до колонок вихідної таблиці з великими літерами потрібні подвійні лапки, наприклад `"Year"`.
- `pgserver` запускає локальний PostgreSQL; дані зберігаються в каталозі, заданому під час запуску сервера.
