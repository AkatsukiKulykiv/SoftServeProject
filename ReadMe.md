                                   # QSPORT

Цей проєкт складається з бекенду (Python + PostgreSQL) та фронтенду (React/Node.js). Для повноцінної роботи системи потрібно налаштувати базу даних, запустити серверну та клієнтську частини, а також увімкнути фонові процеси збору інформації (парсери).

---

## ⚙️ 1. Налаштування Бази Даних та Міграції

Проєкт використовує базу даних PostgreSQL. Перед запуском переконайтеся, що у вас створена локальна база.

1. **Підключення до БД:** Відкрийте файл `database/alembic.ini` та відредагуйте рядок підключення (`sqlalchemy.url`), вказавши дані вашої локальної бази.
2. **Міграції:** Щоб стягнути всі необхідні таблиці в базу даних, виконайте в терміналі наступну команду (це потрібно зробити на самому початку):
   ```bash
   alembic upgrade head
   ```

---

## 🔑 2. Змінні оточення (.env)

Для роботи API та доступу до спортивних даних вам знадобляться ключі. Зареєструйтесь на сайті https://api-sports.io/, створіть API-ключі та додайте їх у змінні оточення.

Створіть файл `.env` у кореневій папці проєкту та заповніть його за цим шаблоном:

```env
# API-Sports Keys
APIKEY1=ваш_api_ключ_1
APIKEY2=6b0a8f23774c79f1c1a0f97159f74bd7
APIKEY3=88687fe1a659f15b432545a4db30b3a7
APIKEY4=1d431440fc5a94bc937aa89685bb581f
APIKEY5=3a3696fd334419ddc79ea426d731dd14
APIKEY6=c26ba1bfcc969a786de0ae5f5a6e37c1
APIKEY8=4fa287ee4a22bdc6c1bf1bf9769edf0e
APIKEY9=9
APIKEY10=10

# Azure Blob Storage
BLOBURL=
SASFOOTBALL=sp=racwdli&st=
SASAFL=sp=racwdli&st=
SASBASEBALL=sp=racwdli&st=
SASBASKETBALL=sp=racwdli&st=
SASFORMULA1=sp=racwdli&st=
SASHANDBALL=sp=racwdli&st=
SASHOCKEY=sp=racwdli&st=
SASMMA=sp=racwdli&st=
SASNBA=sp=racwdli&st=
SASNFL=sp=racwdli&st=
SASRUGBY=sp=racwdli&st=
SASVOLLEYBALL=sp=racwdli&st=
SASNEWS=sp=racwdli&st=

# Security & Mail
SECRET_KEY=
JWT_SECRET_KEY=
MAIL_SERVER=
MAIL_USERNAME=
MAIL_PASSWORD=

# Database
DATABASE_URL=

# Google OAuth
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REDIRECT_URI=
GOOGLE_AUTHORIZATION_BASE_URL=
GOOGLE_TOKEN_URL=
GOOGLE_USER_INFO_URL=
GOOGLE_SCOPES=

# Frontend URLs
FRONT_RESET_PASSWORD_URL=
FRONTEND_NEWS_URL=

# AI Models
TOKEN_HUGGI_FACE=
MODEL_TOKENIZATOR=
```

Створіть файл `.env` у папці frontend та заповніть його за цим шаблоном:

```env
REACT_APP_GOOGLE_CLIENT_ID=
REACT_APP_GOOGLE_REDIRECT_URI=
REACT_APP_BACKEND_URL=
```

---

## 🚀 3. Запуск проєкту на гілці develop

Проєкт вимагає роздільного запуску клієнтської та серверної частин у різних терміналах.

### Запуск Фронтенду
Відкрийте термінал, перейдіть у папку фронтенду та виконайте команду:
```bash
cd frontend
npm run start
```

### Запуск Бекенду
Відкрийте інший термінал у кореневій папці проєкту та запустіть сервер:
```bash
python api/routes/__init__.py
```

---

## 🔄 4. Заповнення бази даних (Скрипти та Парсери)

Щоб на сайті з'явилася інформація (матчі, новини, трансляції), необхідно запустити скрипти збору даних через API. **Дотримуйтесь правильного порядку запуску!**

### 1. Завантаження матчів (Основний шедулер)
Для автоматичного заповнення бази розкладом матчів запустіть цей файл:
```bash
python service/implementation/auto_request_api/shedulers.py
```
> ⚡ **Швидкість запитів:** Якщо потрібно заповнити базу швидше, відкрийте файл `service/implementation/auto_request_api/logic_auto_request.py` та встановіть параметр `"frequency": 1` (запити будуть іти кожну хвилину).  
> **Увага:** Не ставте швидше ніж раз на хвилину, оскільки є високий шанс, що API-ключ заблокують!

### 2. Завантаження новин (Опціонально)
Для парсингу новин локально, за бажанням, запустіть файл:
```bash
python news_scraper/news_scraper.py
```

### 3. Завантаження стрімів (Тільки після матчів!)
Для парсингу посилань на відеотрансляції виконайте команду:
```bash
python streams_scraper/streams_scraper.py
```
> ⚠️ **ВАЖЛИВО:** Цей файл можна запускати **ОБОВ'ЯЗКОВО ТІЛЬКИ ПІСЛЯ** успішного парсингу матчів через шедулер. Інакше парсеру стрімів не буде до чого прив'язувати трансляції, і він просто не працюватиме.