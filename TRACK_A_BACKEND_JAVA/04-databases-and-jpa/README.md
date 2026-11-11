# 🗄️ فاز ۴: پایگاه‌داده و لایه دسترسی داده (Spring Data JPA & Hibernate)

برای یک توسعه‌دهنده بک‌اند، نحوه تعامل با دیتابیس مهم‌ترین عامل در سرعت، پایداری و مقیاس‌پذیری کل سیستم است. در این بخش، نحوه اتصال اصولی به PostgreSQL با استفاده از Hibernate و Spring Data JPA را بررسی می‌کنیم.

---

## 🎯 اهداف این فاز
- [ ] درک لایه‌های ORM (Object-Relational Mapping) و نقش Hibernate.
- [ ] طراحی صحیح Entityها و روابط بین جداول (`@ManyToOne`, `@OneToMany`, `@ManyToMany`).
- [ ] درک تفاوت استراتژی‌های لود: `FetchType.LAZY` در برابر `FetchType.EAGER`.
- [ ] حل همیشگی چالش فاجعه‌بار **N+1 Problem** با `JOIN FETCH` و `@EntityGraph`.
- [ ] مدیریت تراکنش‌ها با انوتیشن `@Transactional` و رفتار انتشار (Propagation).
- [ ] پیاده‌سازی صفحه‌بندی و مرتب‌سازی بهینه با `Pageable`.

---

## 1️⃣ تعریف صحیح یک Entity استاندارد

یک مدل اصولی در JPA باید دارای شناسه، تنظیمات پایگاه‌داده و فیلدهای ممیزی (Auditing) باشد:

```java
@Entity
@Table(name = "orders", indexes = {
    @Index(name = "idx_orders_customer_id", columnList = "customer_id"),
    @Index(name = "idx_orders_status", columnList = "status")
})
@EntityListeners(AuditingEntityListener.class)
public class Order {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, unique = true, length = 36)
    private String orderNumber;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 30)
    private OrderStatus status;

    @ManyToOne(fetch = FetchType.LAZY, optional = false)
    @JoinColumn(name = "customer_id", nullable = false)
    private Customer customer;

    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<OrderItem> items = new ArrayList<>();

    @CreatedDate
    @Column(nullable = false, updatable = false)
    private Instant createdAt;

    // متدهای کمکی برای مدیریت رابطه دوطرفه (Bi-directional helper methods)
    public void addItem(OrderItem item) {
        items.add(item);
        item.setOrder(this);
    }
}
```

> ⚠️ **قانون طلایی روابط:** روابط `@ManyToOne` و `@OneToOne` را **همیشه** به صورت `fetch = FetchType.LAZY` تنظیم کنید! پیش‌فرض JPA برای این روابط `EAGER` است که باعث صدها کوئری پنهان و افت شدید کارایی سرور می‌شود.

---

## 2️⃣ چالش بزرگ N+1 Query Problem و راه‌حل آن

### مشکل چیست؟
فرض کنید ۱۰ سفارش را با کوئری `SELECT * FROM orders` می‌خوانید. اگر برای هر سفارش مشتری آن را هم صدا بزنید (`order.getCustomer().getName()`)، هایبرنیت ۱۰ کوئری مجزا به ازای هر مشتری به دیتابیس می‌زند ($1 + 10 = 11$ کوئری!). اگر ۱۰۰۰ سفارش داشته باشید، ۱۰۰۱ کوئری ارسال می‌شود و دیتابیس فلج خواهد شد!

### راه‌حل اول: استفاده از `JOIN FETCH` در JPQL
```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    @Query("SELECT o FROM Order o JOIN FETCH o.customer JOIN FETCH o.items WHERE o.status = :status")
    List<Order> findAllWithCustomerAndItemsByStatus(@Param("status") OrderStatus status);
}
```
با این روش، هایبرنیت تنها با **یک کوئری تک‌خطی با JOIN** تمام اطلاعات مورد نیاز را از دیتابیس استخراج می‌کند.

### راه‌حل دوم: استفاده از `@EntityGraph`
```java
@EntityGraph(attributePaths = {"customer", "items"})
List<Order> findByStatus(OrderStatus status);
```

---

## 3️⃣ مدیریت تراکنش‌ها (`@Transactional`)

تراکنش تضمین می‌کند که چند عملیات مرتبط با دیتابیس یا همگی با موفقیت ثبت شوند، یا در صورت بروز هر خطایی هیچ اثری از آنها در دیتابیس باقی نماند (Rollback).

```java
@Service
public class OrderFulfillmentService {

    private final OrderRepository orderRepository;
    private final InventoryClient inventoryClient;

    @Transactional
    public void fulfillOrder(Long orderId) {
        Order order = orderRepository.findById(orderId)
            .orElseThrow(() -> new ResourceNotFoundException("سفارش یافت نشد"));

        // کسر موجودی از انبار
        inventoryClient.deductStock(order.getItems());

        // تغییر وضعیت سفارش
        order.setStatus(OrderStatus.CONFIRMED);
        
        // هایبرنیت به صورت اتوماتیک تغییرات Entity پیگیری‌شده (Dirty Checking) را Commit می‌کند
    }

    // برای متدهای صرفاً خواندنی حتماً readOnly = true بگذارید تا هایبرنیت Dirty Checking را غیرفعال کند و مصرف رم کاهش یابد
    @Transactional(readOnly = true)
    public Page<OrderResponseDto> getOrders(Pageable pageable) {
        return orderRepository.findAll(pageable).map(OrderResponseDto::fromEntity);
    }
}
```

---

## 4️⃣ صفحه‌بندی و مرتب‌سازی استاندارد (Pagination)

در دنیای وب هرگز نباید متدی بنویسید که کل یک جدول را بازگرداند (`findAll()` بدون فیلتر و لیمیت، سم پروداکشن است!).

```java
@GetMapping
public ResponseEntity<Page<OrderResponseDto>> listOrders(
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "20") int size,
    @RequestParam(defaultValue = "createdAt,desc") String[] sort
) {
    Pageable pageable = PageRequest.of(page, size, Sort.by(Sort.Direction.DESC, "createdAt"));
    return ResponseEntity.ok(orderService.getOrders(pageable));
}
```

---

## ❓ سوالات متداول مصاحبه‌های فنی این بخش
1. **تفاوت `save()` با `saveAndFlush()` در Spring Data JPA چیست؟**
   - *پاسخ:* متد `save()` موجودیت را در لایه Persistence Context ذخیره می‌کند و دستور `INSERT/UPDATE` SQL معمولاً در انتهای تراکنش (Commit) ارسال می‌شود. متد `saveAndFlush()` بلافاصله کوئری را به پایگاه‌داده ارسال (Flush) می‌کند، هرچند تراکنش هنوز باز است.
2. **چرا نباید انوتیشن `@Transactional` روی متدهای `private` قرار گیرد؟**
   - *پاسخ:* اسپرینگ برای پیاده‌سازی تراکنش از پروکسی‌های داینامیک بر پایه AOP (Aspect-Oriented Programming) استفاده می‌کند. پروکسی نمی‌تواند روی متدهای private قرار گیرد؛ بنابراین تراکنش اعمال نخواهد شد!
