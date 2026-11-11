# 🛠️ پروژه ۳: سیستم فروشگاهی امن با JWT و ردیس (Secure E-Commerce Platform)

یک سیستم جامع فروشگاهی و سفارش‌گذاری آنلاین با تمرکز بر **امنیت، سرعت فوق‌العاده با لایه کشینگ Redis، و کنترل هم‌زمانی در ترنزکشن‌های خرید**. این پروژه یکی از بهترین آیتم‌ها برای درخشش در رزومه است.

---

## 🎯 اهداف یادگیری در این پروژه
- پیاده‌سازی کامل Spring Security 6 با احراز هویت بدون وضعیت (JWT).
- مدیریت توکن‌های دسترسی و توکن‌های تازه‌سازی (Access & Refresh Tokens).
- پیاده‌سازی کنترل دسترسی نقش‌محور (RBAC): دسترسی ادمین در برابر مشتری.
- راه‌اندازی کشینگ پرسرعت برای کاتالوگ محصولات با Redis (`@Cacheable`).
- ذخیره‌سازی سبد خرید موقت کاربران در حافظه سریع Redis با انقضای خودکار (TTL).
- حل مشکل حیاتی **فروش هم‌زمان مازاد بر موجودی (Overselling Race Condition)** با قفل‌های بدبینانه پایگاه‌داده (`Pessimistic Locking`).
- نوشتن تست‌های جامع Integration Test با استفاده از **Testcontainers**.

---

## 📋 معماری و جریان داده سیستم

```
               [ کلاینت / فرانت‌اند ]
                         │
                 JWT Authentication
                         ▼
        ┌───────────────────────────────────┐
        │       Spring Security Filter      │
        └───────────────────────────────────┘
                         │
        ┌────────────────┴──────────────────┐
        ▼                                   ▼
 [ کاتالوگ و محصولات ]              [ سفارش و پرداخت ]
        │                                   │
  Cache-Aside Strategy              تراکنش ACID بانکی
        ▼                                   ▼
 [ Redis Cache ]                    [ PostgreSQL ]
  (پاسخ در ۱ میلی‌ثانیه)              (قفل ردیف با SELECT FOR UPDATE)
```

---

## 🔒 جلوگیری از Overselling با Pessimistic Locking

هنگامی که فقط ۱ عدد از محصولی در انبار مانده و ۱۰ کاربر هم‌زمان دکمه پرداخت را می‌زنند، نباید به بیش از ۱ نفر کالا فروخته شود:

```java
public interface ProductRepository extends JpaRepository<Product, Long> {

    // قفل کردن ردیف کالا در دیتابیس تا پایان تراکنش خرید
    @Lock(LockModeType.PESSIMISTIC_WRITE)
    @Query("SELECT p FROM Product p WHERE p.id = :id")
    Optional<Product> findByIdWithLock(@Param("id") Long id);
}
```

در لایه سرویس:
```java
@Transactional
public OrderResponseDto checkout(Long userId, CheckoutRequest request) {
    for (OrderItemRequest item : request.items()) {
        Product product = productRepository.findByIdWithLock(item.productId())
            .orElseThrow(() -> new ResourceNotFoundException("محصول یافت نشد"));

        if (product.getStockQuantity() < item.quantity()) {
            throw new InsufficientStockException("موجودی محصول ناکافی است: " + product.getName());
        }

        product.setStockQuantity(product.getStockQuantity() - item.quantity());
    }
    // ثبت سفارش و پاکسازی سبد خرید ردیس
}
```

---

## ✅ چک‌لیست پیاده‌سازی پروژه

- [ ] پیاده‌سازی ثبت‌نام و ورود کاربر: هش پسورد با BCrypt و ایجاد جفت توکن Access/Refresh.
- [ ] پیاده‌سازی اندپوینت لاگ‌اوت و قرار دادن توکن در لیست سیاه (Blacklist) در ردیس.
- [ ] راه‌اندازی ردیس در Docker Compose و فعال‌سازی `@EnableCaching`.
- [ ] کش کردن لیست محصولات با `@Cacheable(value = "products", key = "#id")`.
- [ ] ابطال خودکار کش هنگام ویرایش یا اضافه شدن محصول توسط ادمین با `@CacheEvict`.
- [ ] ذخیره داده‌های موقت سبد خرید (Cart) در ردیس با ساختار Hash و TTL برابر ۲۴ ساعت.
- [ ] تراکنش ثبت سفارش با قفل بدبینانه و Rollback در صورت کسری موجودی.
- [ ] نوشتن تست Integration با Testcontainers برای شبیه‌سازی خرید هم‌زمان توسط ۱۰ نخ موازی و اطمینان از عدم فروش مازاد بر موجودی.
