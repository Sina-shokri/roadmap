# 🛠️ پروژه ۴: سیستم توزیع‌شده پردازش سفارشات با Kafka و Docker (Event-Driven Order Processing System)

یک سیستم پیشرفته و مقیاس‌پذیر در سطح Senior Backend بر پایه **معماری رخدادمحور (Event-Driven Architecture)** با استفاده از **Spring Boot 3**، **Apache Kafka**، **PostgreSQL** و **Docker Compose**. این پروژه نشان‌دهنده تسلط عمیق بر مباحث طراحی سیستم‌های توزیع‌شده است.

---

## 🎯 اهداف یادگیری در این پروژه
- پیاده‌سازی ارتباط ناهمگام (Asynchronous Messaging) بین ماژول‌ها یا سرویس‌ها با Apache Kafka.
- پیاده‌سازی تولیدکننده رخداد (Kafka Producer) و مصرف‌کننده (Kafka Consumer) با `@KafkaListener`.
- حل مشکل مصرف تکراری رویدادها با الگوی **Idempotency** (جلوگیری از کسر دوباره موجودی).
- مدیریت خطاهای پیام‌رسانی با صف رخدادهای ناموفق (Dead Letter Queue - DLT) و مکانیزم تلاش مجدد (Retry).
- اجرای محیط کامل سیستم با Docker Compose بدون نیاز به نصب دستی ابزارها.
- پایش سلامت و متریک‌ها با Spring Boot Actuator و Prometheus.

---

## 📋 دیاگرام جریان رخدادها (Event-Driven Flow)

```
[ کلاینت ]
    │ POST /api/v1/orders
    ▼
┌─────────────────────────┐
│      Order Service      │ ─── ثبت سفارش در PostgreSQL (وضعیت PENDING)
└─────────────────────────┘
    │ ارسال رویداد OrderPlacedEvent
    ▼
┌────────────────────────────────────────────────────────┐
│               Apache Kafka (Topic: order-events)       │
└────────────────────────────────────────────────────────┘
    │                               │
    ▼ رویداد به موازات              ▼ رویداد به موازات
┌─────────────────────────┐     ┌─────────────────────────┐
│    Inventory Service    │     │   Notification Service  │
│  (بررسی و کسر انبار)    │     │  (ارسال پیامک و ایمیل)  │
└─────────────────────────┘     └─────────────────────────┘
    │
    ├── اگر موجود بود: ارسال InventoryReservedEvent ──> تغییر وضعیت سفارش به CONFIRMED
    └── اگر کسر بود: ارسال InventoryFailedEvent ──> لغو سفارش و صف DLT
```

---

## 🐳 فایل `docker-compose.yml` کامل برای Kafka و Postgres

در این فایل از نسخه مدرن Kafka در حالت **KRaft** (بدون نیاز به Zookeeper قدیمی و سنگین) استفاده شده است:

```yaml
version: '3.8'

services:
  kafka:
    image: confluentinc/cp-kafka:7.6.0
    container_name: roadmap-kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_NODE_ID: 1
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: 'CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT'
      KAFKA_ADVERTISED_LISTENERS: 'PLAINTEXT://kafka:29092,PLAINTEXT_HOST://localhost:9092'
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_GROUP_INITIAL_REBALANCE_DELAY_MS: 0
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
      KAFKA_PROCESS_ROLES: 'broker,controller'
      KAFKA_CONTROLLER_QUORUM_VOTERS: '1@kafka:29093'
      KAFKA_LISTENERS: 'PLAINTEXT://0.0.0.0:29092,CONTROLLER://0.0.0.0:29093,PLAINTEXT_HOST://0.0.0.0:9092'
      KAFKA_INTER_BROKER_LISTENER_NAME: 'PLAINTEXT'
      KAFKA_CONTROLLER_LISTENER_NAMES: 'CONTROLLER'
      KAFKA_LOG_DIRS: '/tmp/kraft-combined-logs'
      CLUSTER_ID: 'MkU3OEVBNTcwNTJENDM2Qk'

  postgres:
    image: postgres:16-alpine
    container_name: roadmap-postgres
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: orders_db
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password

  kafka-ui:
    image: provectuslabs/kafka-ui:latest
    container_name: roadmap-kafka-ui
    ports:
      - "8085:8080"
    environment:
      KAFKA_CLUSTERS_0_NAME: local
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:29092
```

> 💡 داشبورد بصری کافکا با باز کردن آدرس `http://localhost:8085` در دسترس خواهد بود و امکان مشاهده زنده پیام‌ها و تاپیک‌ها را به شما می‌دهد!

---

## 🔁 پیاده‌سازی Retry و Dead Letter Topic (DLT) در اسپرینگ

اگر سرویس دریافت پیام با خطای گذرا (مثلاً قطعی چند ثانیه‌ای دیتابیس) مواجه شد، اسپرینگ ۳ بار پیام را با تاخیر امتحان می‌کند و در صورت عدم موفقیت، آن را به تاپیک خطای `order-events.DLT` می‌فرستد:

```java
@Component
public class OrderEventConsumer {

    private final InventoryService inventoryService;

    @RetryableTopic(
        attempts = "3",
        backoff = @Backoff(delay = 1000, multiplier = 2.0),
        dltStrategy = DltStrategy.FAIL_ON_ERROR
    )
    @KafkaListener(topics = "order-events", groupId = "inventory-group")
    public void handleOrderPlaced(OrderPlacedEvent event) {
        inventoryService.reserveInventory(event.orderId(), event.items());
    }

    @DltHandler
    public void handleDlt(OrderPlacedEvent event, @Header(KafkaHeaders.RECEIVED_TOPIC) String topic) {
        // ثبت لاگ هشدار برای تیم پشتیبانی جهت بررسی دستی سفارش معیوب
        log.error("سفارش با شکست مواجه شد و به صف خطا منتقل شد: {}", event.orderId());
    }
}
```

---

## ✅ چک‌لیست پیاده‌سازی پروژه

- [ ] بالا آوردن کافکا و دیتابیس با اجرای `docker compose up -d`.
- [ ] ساخت کلاس Producer برای ارسال رویدادها با استفاده از `KafkaTemplate<String, Object>`.
- [ ] ساخت Consumerهای مجزا در Consumer Groupهای مختلف برای استقلال پردازش.
- [ ] ذخیره شناسه رویداد (`eventId` یا `messageKey`) در جدول دیتابیس برای تضمین Idempotency (عدم پردازش تکراری).
- [ ] تست سناریوی Failover: ارسال پیام معیوب و مشاهده هدایت خودکار آن به تاپیک DLT در داشبورد Kafka UI.
- [ ] داکرایز کردن پروژه و اضافه کردن کل سرویس‌ها به `docker-compose.yml`.
