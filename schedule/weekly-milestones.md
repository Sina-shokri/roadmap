# 📅 تقویم هفتگی و نقاط همگام‌سازی یادگیری (Weekly Milestones)

این برنامه به‌گونه‌ای طراحی شده است که در هر هفته، زمان مطالعه به صورت ۵۰-۵۰ یا ۶۰-۴۰ بین **لاین کاربردی جاوا (Track A)** و **لاین مبانی، الگوریتم و دیتابیس (Track B)** تقسیم شود. 

میانگین زمان پیشنهادی: **۱۵ تا ۲۰ ساعت در هفته** (تقریباً ۲ تا ۳ ساعت در روز).

---

## 🗓️ فاز اول: فونداسیون جاوا و پایه‌های علوم کامپیوتر (هفته ۱ تا ۴)

### هفته ۱: شروع ماجراجویی با Java 21 و پیچیدگی الگوریتم‌ها
- **🚀 Track A (جاوا):**
  - [ ] نصب JDK 21 و IntelliJ IDEA Ultimate یا Community.
  - [ ] سیستم انواع داده‌ها (Data Types)، تفاوت Primitives با Wrapper Classes.
  - [ ] ساختار کنترل و حلقه‌ها، نوشتن متدهای تمیز، مدیریت استثناها (`try-catch-finally`, `checked` vs `unchecked`).
  - [ ] [مطالعه داکیومنت فاز ۱ جاوا](../TRACK_A_BACKEND_JAVA/01-java-core-modern/README.md).
- **🧠 Track B (الگوریتم و مبانی):**
  - [ ] مفهوم مرتبه بزرگی (Big-O Notation)، پیچیدگی زمانی (Time Complexity) و فضایی (Space Complexity).
  - [ ] تحلیل سناریوهای $O(1)$، $O(\log n)$، $O(n)$، $O(n \log n)$ و $O(n^2)$.
  - [ ] ساختار حافظه در JVM: تفاوت Stack و Heap و هزینه ساخت شیء جدید در حافظه.
  - [ ] [مطالعه داکیومنت Big-O](../TRACK_B_CS_AND_SYSTEM_DESIGN/01-big-o-and-performance/README.md).
- **🔗 نقطه اتصال (Synergy):**
  - بنچمارک مقایسه سرعت و مصرف حافظه `int[]` در برابر `Integer[]` با حجم ۱ میلیون داده در جاوا.

---

### هفته ۲: شیءگرایی مدرن، Records و آرایه‌ها / لیست‌ها
- **🚀 Track A (جاوا):**
  - [ ] شیءگرایی عمیق: Encapsulation, Inheritance, Polymorphism, Abstraction.
  - [ ] اینترفیس‌ها و کلاس‌های انتزاعی، ویژگی‌های Java 17/21 مانند `record`، `sealed classes` و `Pattern Matching`.
  - [ ] اصول SOLID با مثال‌های ملموس در جاوا.
- **🧠 Track B (الگوریتم و مبانی):**
  - [ ] ساختمان داده‌های خطی: آرایه‌های استاتیک، آرایه‌های پویا (Dynamic Arrays) و لیست‌های پیوندی (Singly & Doubly Linked List).
  - [ ] مقایسه هزینه دسترسی تصادفی $O(1)$ با هزینه درج/حذف در ابتدای لیست.
  - [ ] [مطالعه داکیومنت ساختمان‌داده‌ها](../TRACK_B_CS_AND_SYSTEM_DESIGN/02-data-structures-deep-dive/README.md).
- **🔗 نقطه اتصال (Synergy):**
  - پیاده‌سازی دستی یک `CustomArrayList` جنریک در جاوا و بررسی رشد دینامیک ظرفیت (Capacity Resizing).

---

### هفته ۳: اکوسیستم Collections جاوا و معماری داخلی HashMap
- **🚀 Track A (جاوا):**
  - [ ] جاوا Collections Framework: اینترفیس‌های `List`, `Set`, `Queue`, `Map`.
  - [ ] مقایسه `ArrayList` با `LinkedList`، تفاوت `HashSet` با `TreeSet`، و `HashMap` با `TreeMap`.
  - [ ] قوانین حیاتی قرارداد `equals()` و `hashCode()` و باگ‌های رایج در صورت رعایت نکردن آن.
  - [ ] [مطالعه داکیومنت Collections](../TRACK_A_BACKEND_JAVA/02-collections-and-streams/README.md).
- **🧠 Track B (الگوریتم و مبانی):**
  - [ ] جدول‌های درهم‌سازی (Hash Tables): تابع هش، توزیع یکنواخت، و پدیده تصادم (Collision).
  - [ ] روش‌های حل تصادم: Chaining و Open Addressing.
  - [ ] بررسی چگونگی تبدیل باکت‌های `HashMap` در جاوا از LinkedList به Red-Black Tree در صورت شلوغ شدن باکت ($O(n) \to O(\log n)$).
- **🔗 نقطه اتصال (Synergy):**
  - حل مسئله مشهور Two Sum و Group Anagrams در LeetCode با استفاده هوشمندانه از `HashMap`.

---

### هفته ۴: برنامه‌نویسی تابعی، استریم‌ها و تکنیک‌های جستجو
- **🚀 Track A (جاوا):**
  - [ ] عبارت‌های لامبدا (Lambdas) و اینترفیس‌های تابعی (`Function`, `Predicate`, `Consumer`, `Supplier`).
  - [ ] کار با Streams API: عملیات واسطه‌ای (`filter`, `map`, `flatMap`, `sorted`) و عملیات پایانی (`collect`, `reduce`).
  - [ ] کار ایمن با `Optional` و جلوگیری همیشگی از `NullPointerException`.
- **🧠 Track B (الگوریتم و مبانی):**
  - [ ] الگوریتم‌های جستجو و مرتب‌سازی: Binary Search، Merge Sort و Quick Sort.
  - [ ] الگوی حل مسئله Two Pointers و Sliding Window.
  - [ ] مقایسه پرفورمنس پردازش داده با Stream در برابر حلقه `for` سنتی.
  - [ ] [مطالعه داکیومنت الگوهای الگوریتمی](../TRACK_B_CS_AND_SYSTEM_DESIGN/03-algorithmic-patterns/README.md).
- **🛠️ خروجی این فاز:**
  - [ ] تکمیل و تحویل [پروژه ۱: ابزار CLI بنچمارک ساختمان داده‌ها](../projects/project-01-performance-benchmarker/README.md).

---

## 🗓️ فاز دوم: ورود به پایگاه‌داده، SQL عمیق و Spring Boot (هفته ۵ تا ۸)

### هفته ۵: اصول پایگاه‌داده رابطه‌ای و SQL خام
- **🚀 Track A (بک‌اند):**
  - [ ] ابزار بیلد Maven: مفهوم `pom.xml`، لایه‌های وابستگی (Dependencies) و چرخه حیات (Lifecycle).
  - [ ] ساختار یک اپلیکیشن جاوا با لایه‌بندی تمیز (Controller, Service, Repository, DTO).
- **🧠 Track B (پایگاه‌داده و SQL):**
  - [ ] مبانی رابطه داده‌ها (1:1, 1:N, M:N) و نرمال‌سازی (1NF, 2NF, 3NF).
  - [ ] زبان SQL عمیق: دستورات DDL و DML. انواع اتصال‌ها (`INNER JOIN`, `LEFT JOIN`, `FULL JOIN`, `CROSS JOIN`).
  - [ ] گروه‌بندی و تجمیع داده‌ها (`GROUP BY`, `HAVING`, `COUNT`, `SUM`, `AVG`).
  - [ ] [مطالعه داکیومنت عمیق SQL](../TRACK_B_CS_AND_SYSTEM_DESIGN/04-sql-fundamentals-and-databases/README.md).
- **🔗 نقطه اتصال (Synergy):**
  - نوشتن کوئری‌های پیچیده تحلیلی روی دیتابیس آزمایشی PostgreSQL بدون استفاده از هیچ فریم‌ورکی.

---

### هفته ۶: ایندکس‌ها، کوئری‌های بهینه و ورود به Spring Boot 3
- **🚀 Track A (اسپرینگ بوت):**
  - [ ] درک فلسفه اسپرینگ: Inversion of Control (IoC) و Dependency Injection (DI).
  - [ ] نحوه کار Beanها و اسکوپ‌های آنها (`@Component`, `@Service`, `@Repository`, `@Autowired`).
  - [ ] ساخت اولین کنترلر REST با `@RestController`, `@GetMapping`, `@PostMapping` و DTOها.
  - [ ] [مطالعه داکیومنت مقدمات Spring Boot](../TRACK_A_BACKEND_JAVA/03-spring-boot-essentials/README.md).
- **🧠 Track B (پایگاه‌داده و ایندکس):**
  - [ ] ساختار ذخیره‌سازی داده روی دیسک: چرا اسکن کل جدول (Seq Scan) کند است؟
  - [ ] درخت‌های B-Tree و نحوه کار Index در دیتابیس.
  - [ ] ایندکس‌های ترکیبی (Composite Indexes) و قانون چپ‌ترین ستون (Leftmost Prefix Rule).
  - [ ] آنالیز پرفورمنس کوئری‌ها با دستور `EXPLAIN ANALYZE` در PostgreSQL.
- **🔗 نقطه اتصال (Synergy):**
  - دیدن اثر واقعی ایندکس با اضافه کردن ۱ میلیون رکورد و کاهش زمان اجرای کوئری از ۵۰۰ میلی‌ثانیه به ۲ میلی‌ثانیه.

---

### هفته ۷: ادغام اسپرینگ با دیتابیس (Spring Data JPA) و ترنزکشن‌ها
- **🚀 Track A (اسپرینگ و دیتابیس):**
  - [ ] مفهوم ORM و Hibernate: انتیتی‌ها (`@Entity`, `@Table`, `@Id`, `@GeneratedValue`).
  - [ ] روابط بین جداول در JPA (`@ManyToOne`, `@OneToMany`, `@ManyToMany`).
  - [ ] استفاده از `JpaRepository`، متدهای کوئری‌ساز اتوماتیک و کوئری‌های `@Query` با JPQL و Native SQL.
  - [ ] حل معمای بزرگ معماران جاوا: **رفع چالش N+1 Problem** با `JOIN FETCH` و `EntityGraph`.
  - [ ] [مطالعه داکیومنت JPA و دیتابیس](../TRACK_A_BACKEND_JAVA/04-databases-and-jpa/README.md).
- **🧠 Track B (تراکنش‌های ACID و ایزولاسیون):**
  - [ ] اصول چهارگانه ACID (Atomicity, Consistency, Isolation, Durability).
  - [ ] پدیده‌های ناهنجاری هم‌زمانی: Dirty Read, Non-repeatable Read, Phantom Read.
  - [ ] سطوح ایزولاسیون ترنزکشن‌ها: Read Uncommitted, Read Committed, Repeatable Read, Serializable.
  - [ ] مدیریت تراکنش‌ها در جاوا با انوتیشن `@Transactional` و رفتار انتشار (Propagation).
- **🔗 نقطه اتصال (Synergy):**
  - شبیه‌سازی تراکنش انتقال وجه بانکی و تست رفتارهای Rollback و ایزولاسیون.

---

### هفته ۸: اعتبارسنجی، مدیریت خطای استاندارد و تحویل پروژه دوم
- **🚀 Track A (اسپرینگ):**
  - [ ] اعتبارسنجی ورودی‌ها با Bean Validation (`@NotNull`, `@Size`, `@Email`, `@Pattern`).
  - [ ] مدیریت متمرکز خطاها با `@RestControllerAdvice` و `@ExceptionHandler` و استاندارد RFC 7807 (Problem Details).
  - [ ] مستندسازی اتوماتیک API با OpenAPI / Swagger.
- **🧠 Track B (الگوریتم و طراحی پایگاه داده):**
  - [ ] تکنیک‌های صفحه‌بندی (Pagination): مقایسه Offset-based Pagination با Keyset/Cursor-based Pagination برای جداول میلیونی.
  - [ ] مفاهیم Soft Delete در مقابل Hard Delete و اثرات آن روی ایندکس‌ها.
- **🛠️ خروجی این فاز:**
  - [ ] پیاده‌سازی کامل [پروژه ۲: وب‌سرویس REST مدیریت وظایف با PostgreSQL](../projects/project-02-task-manager-rest-api/README.md).

---

## 🗓️ فاز سوم: امنیت، کارایی، ردیس و هم‌زمانی (هفته ۹ تا ۱۲)

### هفته ۹: امنیت بک‌اند، معماری Spring Security و احراز هویت
- **🚀 Track A (امنیت):**
  - [ ] فیلترچِین اسپرینگ سکوریتی (`SecurityFilterChain`) و تفکیک Authentication از Authorization.
  - [ ] هش کردن امن پسوردها با BCrypt و الگوریتم‌های مدرن.
  - [ ] پیاده‌سازی Stateless Authentication با JSON Web Tokens (Access Token و Refresh Token).
  - [ ] مدیریت نقش‌ها و دسترسی‌ها (RBAC) با `@PreAuthorize`.
  - [ ] [مطالعه داکیومنت امنیت](../TRACK_A_BACKEND_JAVA/05-security-and-auth/README.md).
- **🧠 Track B (مفاهیم امنیت و الگوریتم‌های هش):**
  - [ ] تفاوت Encryption (رمزنگاری متقارن/نامتقارن) با Hashing و Salting.
  - [ ] آسیب‌پذیری‌های رایج وب (OWASP Top 10): SQL Injection, XSS, CSRF, IDOR و راه‌های دفاعی در جاوا.
- **🔗 نقطه اتصال (Synergy):**
  - نوشتن فیلتر سفارشی `JwtAuthenticationFilter` و قرار دادن آن در زنجیره امنیتی اسپرینگ.

---

### هفته ۱۰: لایه کشینگ سریع با Redis و الگوریتم‌های Caching
- **🚀 Track A (ردیس در اسپرینگ):**
  - [ ] راه‌اندازی Redis با داکر و اتصال Spring Boot به آن از طریق Spring Data Redis.
  - [ ] استفاده از انوتیشن‌های کش اسپرینگ: `@Cacheable`, `@CachePut`, `@CacheEvict`.
  - [ ] سریالایزیشن داده‌ها در ردیس و تنظیم زمان انقضا (TTL).
- **🧠 Track B (ساختار داده‌های ردیس و الگوریتم کش):**
  - [ ] دیتا تایپ‌های داخلی ردیس: Strings, Hashes, Lists, Sets, Sorted Sets.
  - [ ] استراتژی‌های کشینگ: Cache-Aside, Write-Through, Write-Behind.
  - [ ] الگوریتم‌های بازیافت حافظه کش (Eviction Policies): LRU (Least Recently Used) و LFU.
  - [ ] خطرات کشینگ: Cache Stampede, Cache Penetration, Cache Avalanche و راهکارهای پیشگیری.
- **🔗 نقطه اتصال (Synergy):**
  - پیاده‌سازی دستی الگوریتم LRU Cache با ترکیب `HashMap` و `DoublyLinkedList` در جاوا.

---

### هفته ۱۱: چندنخی، هم‌زمانی و مقایسه نخ‌های سنتی با Virtual Threads
- **🚀 Track A (چندنخی در اسپرینگ):**
  - [ ] اجرای متدهای غیرهمگام با انوتیشن `@Async` و تنظیم ThreadPoolTaskExecutor سفارشی.
  - [ ] استفاده از قابلیت انقلاب‌گونه Virtual Threads در Java 21 برای مقیاس‌پذیری خیره‌کننده I/O.
- **🧠 Track B (مبانی Concurrency و قفل‌ها):**
  - [ ] مفهوم Process و Thread، شرایط رقابتی (Race Conditions) و ناحیه بحرانی (Critical Section).
  - [ ] متغیرهای اتمیک (`AtomicInteger`)، کلمه کلیدی `volatile` و بلوک `synchronized`.
  - [ ] ابزارهای پیشرفته پکیج `java.util.concurrent`: `ReentrantLock`, `CountDownLatch`, `Semaphore`, `ConcurrentHashMap`.
  - [ ] خطرات بن‌بست (Deadlock) و ۴ شرط گره‌خوردن نیدل‌ها.
  - [ ] [مطالعه داکیومنت هم‌زمانی](../TRACK_B_CS_AND_SYSTEM_DESIGN/05-concurrency-and-threads/README.md).
- **🔗 نقطه اتصال (Synergy):**
  - تست پایداری موجودی کالا در حین خرید هم‌زمان ۱۰۰ کاربر و جلوگیری از فروش تکراری (Over-selling) با قفل‌های بدبینانه (Pessimistic Locking) در دیتابیس.

---

### هفته ۱۲: تست‌نویسی حرفه‌ای و تحویل پروژه سوم
- **🚀 Track A (تست‌نویسی):**
  - [ ] تست واحد (Unit Testing) با JUnit 5 و فریم‌ورک ماک‌سازی Mockito.
  - [ ] تست ادغام (Integration Testing) لایه وب با `@WebMvcTest` و MockMvc.
  - [ ] استفاده از کتابخانه بی‌نظیر Testcontainers برای بالا آوردن دیتابیس واقعی PostgreSQL و Redis در تست‌ها.
  - [ ] [مطالعه داکیومنت تست و پروداکشن](../TRACK_A_BACKEND_JAVA/06-testing-and-production/README.md).
- **🧠 Track B (الگوهای معماری):**
  - [ ] معماری پیازی و تمیز (Clean Architecture / Hexagonal Architecture).
  - [ ] تفکیک دقیق لایه‌ها و قوانین معکوس‌سازی وابستگی.
- **🛠️ خروجی این فاز:**
  - [ ] تکمیل و تحویل [پروژه ۳: سیستم فروشگاهی امن با JWT و ردیس](../projects/project-03-secure-ecommerce-platform/README.md).

---

## 🗓️ فاز چهارم: معماری سیستم‌های توزیع‌شده، داکر و کافکا (هفته ۱۳ تا ۱۶)

### هفته ۱۳: کانتینرسازی با Docker و زیرساخت پروداکشن
- **🚀 Track A (داکر و مانیتورینگ):**
  - [ ] نوشتن Dockerfile چندمرحله‌ای بهینه (Multi-stage build) با JDK و JRE سبک (Eclipse Temurin Alpine).
  - [ ] نوشتن فایل `docker-compose.yml` کامل شامل سرویس اپ، دیتابیس، ردیس و pgAdmin.
  - [ ] راه‌اندازی Spring Boot Actuator برای بررسی سلامت سیستم (Health Check و Metrics).
- **🧠 Track B (مفاهیم استقرار و شبکه):**
  - [ ] شبکه داکر (Bridge, Host, Overlay)، والیوم‌ها و چرخه حیات کانتینر.
  - [ ] مفاهیم Reverse Proxy با Nginx و لود بالانسینگ (Round Robin, Least Connections).

---

### هفته ۱۴: سیستم‌های پیام‌رسانی و رخدادمحور با Apache Kafka
- **🚀 Track A (کافکا در اسپرینگ):**
  - [ ] ادغام Spring Boot با Spring for Apache Kafka.
  - [ ] ساخت `KafkaProducer` برای انتشار رویدادها و `KafkaConsumer` با `@KafkaListener`.
  - [ ] مدیریت خطا، Retry و صف‌های خطا (Dead Letter Queue - DLT).
- **🧠 Track B (مفاهیم معماری Message Broker):**
  - [ ] مقایسه پیام‌رسان صف سنتی (RabbitMQ) با Event Streaming لاگ‌محور (Kafka).
  - [ ] مفاهیم تاپیک (Topic)، پارتیشن (Partition)، Consumer Group و آفست (Offset).
  - [ ] ضمانت‌های ارسال پیام: At-most-once, At-least-once, Exactly-once.

---

### هفته ۱۵: تسلط بر طراحی سیستم‌های مقیاس‌پذیر (System Design)
- **🚀 Track A و 🧠 Track B (ترکیب کامل برای مصاحبه‌های سنیور):**
  - [ ] قضیه CAP و مدل PACELC: چه زمانی Consistency فدای Availability می‌شود؟
  - [ ] استراتژی‌های مقیاس‌پذیری پایگاه‌داده: Read Replicas، دیتابیس شاردینگ (Horizontal Sharding) و پارتیشن‌بندی.
  - [ ] طراحی الگوهای توزیع‌شده: Circuit Breaker (Resilience4j)، Rate Limiting (با Bucket4j یا Redis).
  - [ ] الگوی تراکنش‌های توزیع‌شده (Saga Pattern: Choreography vs Orchestration).
  - [ ] [مطالعه داکیومنت جامع System Design](../TRACK_B_CS_AND_SYSTEM_DESIGN/06-system-design-mastery/README.md).

---

### هفته ۱۶: تکمیل پروژه پایانی و جمع‌بندی رزومه
- **🛠️ خروجی نهایی:**
  - [ ] راه‌اندازی و اجرای [پروژه ۴: پلتفرم توزیع‌شده پردازش رویدادها با Kafka و Docker](../projects/project-04-scalable-event-system/README.md).
  - [ ] آماده‌سازی مستندات و ریپازیتوری‌های گیت‌هاب جهت ارائه در رزومه و مصاحبه‌های کاری.
