# 🚀 مسیر تخصصی مهندسی کاربردی جاوا و اسپرینگ بوت (Track A)

این بخش ستون فقرات ساخت نرم‌افزارهای تجاری و سازمانی با زبان جاوا است. هدف این لاین، آموزش عملی، تمیز و استاندارد از نسخه مدرن جاوا (Java 21 LTS) تا اکوسیستم کامل Spring Boot 3 و استقرار آن در محیط پروداکشن است.

---

## 📚 سرفصل‌های این مسیر

1. **[01. هسته جاوا مدرن (Modern Java Core)](file:///Users/amirabaris/Documents/roadmap/TRACK_A_BACKEND_JAVA/01-java-core-modern/README.md)**
   - سینتکس تمیز، ساختارهای کنترلی، مدیریت استثناها.
   - شیءگرایی اصولی، اینترفیس‌ها، کلاس‌های انتزاعی، جنریک‌ها (Generics).
   - ویژگی‌های مدرن Java 17 و Java 21: Records، Pattern Matching، Sealed Classes.
   - اصول کد تمیز (Clean Code) و اصول پنج‌گانه SOLID.

2. **[02. فریم‌ورک کالکشن‌ها و برنامه‌نویسی تابعی (Collections & Streams)](file:///Users/amirabaris/Documents/roadmap/TRACK_A_BACKEND_JAVA/02-collections-and-streams/README.md)**
   - کالکشن‌های پرکاربرد: `List`, `Set`, `Map`, `Queue` و پیاده‌سازی‌های استاندارد آنها.
   - قرارداد طلایی `equals()` و `hashCode()`.
   - برنامه‌نویسی تابعی با Lambdas و Functional Interfaces.
   - پردازش حجم بالای داده به شیوه Declarative با Streams API.
   - هندلینگ مقادیر خالی با `Optional`.

3. **[03. مبانی و هسته اسپرینگ بوت ۳ (Spring Boot Essentials)](file:///Users/amirabaris/Documents/roadmap/TRACK_A_BACKEND_JAVA/03-spring-boot-essentials/README.md)**
   - معماری هسته اسپرینگ: IoC Container، Dependency Injection و Bean Lifecycle.
   - پیکربندی اتوماتیک (Spring Boot Auto-configuration) و مدیریت کانفیگ‌ها (`application.yml`).
   - ساخت RESTful APIهای مدرن با استاندارد HTTP status codeها.
   - اعتبارسنجی ورودی‌ها با Hibernate Validator و مدیریت خطاهای سراسری (Global Exception Handling).

4. **[04. پایگاه‌داده و لایه دسترسی داده (Databases & JPA)](file:///Users/amirabaris/Documents/roadmap/TRACK_A_BACKEND_JAVA/04-databases-and-jpa/README.md)**
   - اتصال به PostgreSQL با Spring Data JPA و Hibernate ORM.
   - تعریف Entityها، کلیدهای اصلی، روابط `@ManyToOne`, `@OneToMany`, `@ManyToMany`.
   - حل مشکل مهلک N+1 با `JOIN FETCH` و `@EntityGraph`.
   - ترنزکشن‌های دیتابیس با `@Transactional` و مدیریت ایزولاسیون و Rollback.

5. **[05. امنیت و احراز هویت (Security & Auth)](file:///Users/amirabaris/Documents/roadmap/TRACK_A_BACKEND_JAVA/05-security-and-auth/README.md)**
   - معماری فیلترچِین Spring Security 6.
   - احراز هویت با JWT (Access Token و Refresh Token).
   - هش کردن پسورد با BCrypt.
   - کنترل دسترسی بر پایه نقش (Role-Based Access Control - RBAC).

6. **[06. تست‌نویسی حرفه‌ای و آماده‌سازی پروداکشن (Testing & Production)](file:///Users/amirabaris/Documents/roadmap/TRACK_A_BACKEND_JAVA/06-testing-and-production/README.md)**
   - تست‌های واحد (Unit Testing) با JUnit 5 و Mockito.
   - تست‌های یکپارچگی (Integration Testing) با Testcontainers و بالا آوردن سرویس‌های واقعی.
   - کانتینرسازی با Docker چند مرحله‌ای.
   - پایش وضعیت سیستم با Spring Boot Actuator.

---

## 🛠️ پیش‌نیازهای نرم‌افزاری
- نصب **JDK 21** (توصیه: Eclipse Temurin یا Amazon Corretto).
- محیط توسعه: **IntelliJ IDEA** (نسخه Ultimate یا Community).
- ابزار مدیریت پروژه و بیلد: **Maven** (یا Gradle).
- ابزار کانتینرسازی: **Docker Desktop** یا OrbStack.
- نرم‌افزار مدیریت پایگاه‌داده: **DBeaver** یا DataGrip.
