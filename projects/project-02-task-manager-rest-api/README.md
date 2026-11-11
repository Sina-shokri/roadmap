# 🛠️ پروژه ۲: وب‌سرویس REST مدیریت وظایف (Task Manager REST API)

یک وب‌سرویس بک‌اند کامل، تمیز و استاندارد با استفاده از **Spring Boot 3**، **Spring Data JPA** و دیتابیس **PostgreSQL** برای مدیریت پروژه‌ها، وظایف و برچسب‌ها.

---

## 🎯 اهداف یادگیری در این پروژه
- پیاده‌سازی معماری تمیز لایه‌ای (Controller $\to$ Service $\to$ Repository $\to$ DTO).
- مدیریت دیتابیس واقعی PostgreSQL با Docker Compose و مایگریشن با **Flyway**.
- تعریف صحیح روابط `@ManyToOne` و `@OneToMany` با لود تنبل (`FetchType.LAZY`).
- پیاده‌سازی اعتبارسنجی ورودی‌ها و پاسخگویی به خطاهای استاندارد (Problem Details).
- پیاده‌سازی صفحه‌بندی و فیلترینگ چندمعیاره با `Specification` یا Spring Data.
- مستندسازی خودکار با Swagger UI.

---

## 📋 مدل داده و سناریوی بیزینس

```
┌──────────────────┐           ┌──────────────────┐
│     projects     │ 1       N │      tasks       │
├──────────────────┤───────────├──────────────────┤
│ id (PK)          │           │ id (PK)          │
│ name             │           │ title            │
│ description      │           │ description      │
│ created_at       │           │ status (ENUM)    │
└──────────────────┘           │ priority (ENUM)  │
                               │ due_date         │
                               │ project_id (FK)  │
                               │ created_at       │
                               └──────────────────┘
```

### اندپوینت‌های مورد نیاز (REST Endpoints):
- `POST /api/v1/projects` - ایجاد پروژه جدید
- `GET /api/v1/projects` - لیست پروژه‌ها همراه با تعداد تسک‌های باز
- `POST /api/v1/projects/{projectId}/tasks` - ایجاد تسک ذیل یک پروژه
- `GET /api/v1/tasks` - جستجو و فیلتر پیشرفته تسک‌ها (بر اساس `status`, `priority`, `projectId` به همراه صفحه‌بندی `page`, `size`, `sort`)
- `PATCH /api/v1/tasks/{id}/status` - تغییر وضعیت تسک (`TODO`, `IN_PROGRESS`, `DONE`, `CANCELLED`)
- `DELETE /api/v1/tasks/{id}` - حذف تسک (کد ۲04)

---

## 🐳 فایل `docker-compose.yml` آماده برای دیتابیس

دوست شما با اجرای دستور `docker compose up -d` یک دیتابیس آماده خواهد داشت:

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:16-alpine
    container_name: taskmanager-postgres
    environment:
      POSTGRES_DB: taskmanager_db
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

---

## ✅ چک‌لیست پیاده‌سازی پروژه

- [ ] راه‌اندازی پروژه در [start.spring.io](https://start.spring.io) با Java 21 و وابستگی‌های Web, Data JPA, Postgres, Validation, Flyway, Lombok (یا بدون لامبوک با Records).
- [ ] نوشتن اسکریپت اولیه Flyway در `src/main/resources/db/migration/V1__init_schema.sql`.
- [ ] ساخت کلاس‌های Entity با ایندکس‌های مناسب روی ستون‌های پرجستجو (`status`, `project_id`).
- [ ] تعریف DTOهای مجزا برای Request و Response (بدون درز مستقیم Entityها).
- [ ] پیاده‌سازی متد فیلترینگ و صفحه‌بندی در Service و بازگرداندن `Page<TaskResponseDto>`.
- [ ] پیاده‌سازی `@RestControllerAdvice` برای مدیریت خطای ۴۰۴ و ارورهای ولیدیشن ۴۰۰.
- [ ] پیکربندی Swagger با دسترسی به آدرس `http://localhost:8080/swagger-ui.html`.
- [ ] نوشتن حداقل ۵ تست واحد با Mockito برای لایه `TaskService`.
