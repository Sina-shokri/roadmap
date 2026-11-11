# 🧠 مسیر تخصصی مبانی علوم کامپیوتر، الگوریتم، SQL و طراحی سیستم (Track B)

یک برنامه‌نویس جاوا که فریم‌ورک اسپرینگ را می‌داند اما از عملکرد داخلی حافظه، تحلیل پیچیدگی کد، ساختار داخلی دیتابیس (SQL) و اصول معماری مقیاس‌پذیر بی‌خبر است، با افزایش ترافیک سیستم دچار بحران خواهد شد.

این لاین اختصاصاً برای پرورش **تفکر مهندسی، حل مسئله، بهینه‌سازی پرفورمنس، تسلط بی‌نقص بر SQL و آمادگی برای طراحی سیستم‌های توزیع‌شده (System Design)** طراحی شده است.

---

## 📚 سرفصل‌های این مسیر

1. **[01. نماد Big-O و تحلیل کارایی (Big-O & Performance)](./01-big-o-and-performance/README.md)**
   - تحلیل پیچیدگی زمانی (Time Complexity) و فضایی (Space Complexity).
   - سناریوهای بهترین، میانگین و بدترین حالت ($O, \Omega, \Theta$).
   - رفتار حافظه در JVM: مقایسه Stack با Heap و پرفورمنس Primitives در برابر Objects.

2. **[02. کالبدشکافی ساختمان‌داده‌ها در جاوا (Data Structures Deep Dive)](./02-data-structures-deep-dive/README.md)**
   - آرایه‌ها و لیست‌های پیوندی: مقایسه موشکافانه پیاده‌سازی داخلی `ArrayList` و `LinkedList`.
   - پشته و صف (Stack & Queue): پیاده‌سازی و موارد استفاده در صف درخواست‌ها.
   - جداول درهم‌سازی (Hash Tables): تابع هش، تصادم‌ها و بررسی چگونگی تبدیل باکت‌ها به درخت سرخ-سیاه (Red-Black Tree) در `HashMap` جاوا.

3. **[03. الگوهای الگوریتمی متداول بک‌اند (Algorithmic Patterns)](./03-algorithmic-patterns/README.md)**
   - الگوی دو اشاره‌گر (Two Pointers) و پنجره لغزان (Sliding Window).
   - اشاره‌گرهای سریع و کند (Fast & Slow Pointers / Tortoise and Hare).
   - جستجوی دودویی (Binary Search) و مشتقات آن.
   - الگوریتم‌های پرکاربرد گراف و درخت: پیمایش سطحی (BFS) و عمقی (DFS).

4. **[04. اصول عمیق پایگاه‌داده و تسلط بر SQL (SQL Fundamentals & Databases)](./04-sql-fundamentals-and-databases/README.md)**
   - اصول طراحی پایگاه‌داده رابطه‌ای و نرمال‌سازی (1NF, 2NF, 3NF).
   - انواع اتصالات (INNER, LEFT, RIGHT, FULL, CROSS JOIN) و سناریوهای تجاری.
   - توابع تحلیلی و پنجره‌ای (Window Functions: `ROW_NUMBER`, `RANK`, `DENSE_RANK`, `LEAD`, `LAG`).
   - ساختار ایندکس‌ها: چرا B-Tree انتخاب شد؟ ایندکس‌های کامپوزیت و دام‌های کندی کوئری.
   - ترنزکشن‌های ACID و سطوح ایزولاسیون (Dirty Read, Non-Repeatable Read, Phantom Read).
   - تحلیل کوئری با `EXPLAIN ANALYZE` در PostgreSQL.

5. **[05. هم‌زمانی، چندنخی و ترد‌های مجازی (Concurrency & Threads)](./05-concurrency-and-threads/README.md)**
   - شرایط رقابتی (Race Conditions) و ناحیه بحرانی.
   - ابزارهای هم‌زمانی: `volatile`, `AtomicInteger`, `synchronized`, `ReentrantLock`.
   - نخ‌های سنتی سیستم‌عامل در برابر **Virtual Threads** در Java 21 (Project Loom).
   - بن‌بست (Deadlock) و استراتژی‌های پیشگیری.

6. **[06. تسلط بر طراحی سیستم (System Design Mastery)](./06-system-design-mastery/README.md)**
   - مقیاس‌پذیری عمودی در برابر افقی (Horizontal vs Vertical Scaling).
   - لود بالانسرها، Reverse Proxies و الگوریتم‌های توزیع بار.
   - استراتژی‌های Caching با Redis (Cache-Aside, Write-Through).
   - قضیه CAP و مدل PACELC در سیستم‌های توزیع‌شده.
   - پیام‌رسانی و صف‌ها: مقایسه RabbitMQ و Apache Kafka.
