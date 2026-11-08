# LabBank — S1 Customer & Account Management

سرویس S1 مرجع رسمی مشتریان و حساب‌های LabBank است. این سرویس ثبت مشتری، مدیریت وضعیت حساب، احراز هویت مشتری و قابلیت‌های مرتبط با حساب را ارائه می‌کند.

## فناوری‌ها

- Python 3.12.x
- FastAPI و Uvicorn
- SQLAlchemy 2.x
- PostgreSQL (پیشنهادی/پیش‌فرض برای اجرای اصلی)
- JWT برای احراز هویت
- pytest برای آزمون‌های خودکار

## راه‌اندازی محلی

1. یک محیط مجازی Python بسازید:

   **Windows (PowerShell):**

   ```powershell
   py -3.12 -m venv .venv
   .\.venv\Scripts\python.exe -m pip install -r requirements-dev.txt
   ```

   **Linux / macOS:**

   ```bash
   python3.12 -m venv .venv
   .venv/bin/python -m pip install -r requirements-dev.txt
   ```

2. برای اجرای مستقیم، PostgreSQL را آماده کنید و متغیرهای محیطی را در همان ترمینال تنظیم کنید. مقدار `JWT_SECRET` باید تصادفی و حداقل ۱۶ کاراکتری باشد. فایل `.env.example` نمونه‌ی همین تنظیمات است؛ کپی‌کردن آن به `.env` به‌تنهایی باعث بارگذاری خودکار متغیرها هنگام اجرای مستقیم Uvicorn نمی‌شود.

   **Windows (PowerShell):**

   ```powershell
   $env:DATABASE_URL = "postgresql+psycopg2://labbank:labbank@localhost:5432/s1_accounts"
   $env:JWT_SECRET = "replace-with-a-random-secret-at-least-16-chars"
   ```

   **Linux / macOS:**

   ```bash
   export DATABASE_URL="postgresql+psycopg2://labbank:labbank@localhost:5432/s1_accounts"
   export JWT_SECRET="replace-with-a-random-secret-at-least-16-chars"
   ```

3. سرویس را از ریشه پروژه اجرا کنید:

   ```bash
   uvicorn main:app --host 0.0.0.0 --port 8081
   ```

   پورت پیش‌فرض S1 در مشخصات پروژه `8081` است. در Docker Compose، پورت میزبان `8081` به پورت داخلی کانتینر (`8080`) نگاشت شده است.

## اجرای Docker Compose

برای اجرای Compose، فایل `.env` را از روی `.env.example` بسازید و `JWT_SECRET` را تغییر دهید. Docker Compose فایل `.env` ریشه پروژه را برای مقداردهی متغیرهای تعریف‌شده در `docker-compose.yml` می‌خواند:

```bash
docker compose up --build
```

برای توقف سرویس:

```bash
docker compose down
```

## Endpointهای پایه

| Method | Path | کاربرد |
|---|---|---|
| `GET` | `/health` | بررسی پاسخ‌گویی فرایند |
| `GET` | `/ready` | بررسی آمادگی وابستگی دیتابیس |
| `POST` | `/api/v1/users` | ثبت مشتری |
| `POST` | `/api/v1/auth/login` | ورود مشتری |
| `POST` | `/api/v1/auth/refresh` | تمدید توکن |
| `GET` | `/api/v1/auth/me` | دریافت پروفایل کاربر واردشده |

جزئیات مسیرها و قراردادهای مرتبط در کد و مستندات پوشه `docs/` قرار دارند.

## اجرای تست‌ها

```bash
python -m pytest -q
```

تست‌های خودکار از SQLite موقت استفاده می‌کنند و تنظیمات تست در `tests/conftest.py` انجام می‌شود؛ برای اجرای تست‌ها به دیتابیس PostgreSQL نیاز نیست.

## اجرای رابط کاربری

رابط کاربری در پوشه `frontend/` قرار دارد و جدا از سرویس API اجرا می‌شود. پس از نصب Node.js و وابستگی‌های npm:

```bash
cd frontend
npm ci
npm run dev
```

مقدار API در `frontend/.env.example` به `http://127.0.0.1:8081` اشاره می‌کند.

## ساختار مهم پروژه

- `main.py`: ساخت برنامه FastAPI و ثبت Routerها
- `app/routers/`: مسیرهای API مربوط به احراز هویت و کاربران
- `app/models.py`: مدل‌های داده
- `app/database.py`: اتصال دیتابیس و اجرای Migrationها
- `migrations/`: تغییرات ساختار دیتابیس
- `tests/`: تست‌های Unit و Integration
- `tests-api/`: سناریوهای API برای Bruno
- `docs/`: قراردادها و مستندات تکمیلی
- `frontend/`: رابط کاربری React

## تنظیمات مهم محیطی

تنظیمات اصلی در `.env.example` آمده‌اند. از ثبت Secret واقعی در Git خودداری کنید. فایل `.env` نباید به مخزن اضافه شود.
