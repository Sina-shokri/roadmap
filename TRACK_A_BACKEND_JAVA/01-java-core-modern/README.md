# ☕ فاز ۱: هسته جاوا مدرن (Java 21 LTS Mastery)

برای اینکه در فریم‌ورک اسپرینگ‌بوت به مشکل نخورید، باید جاوا را به‌صورت اصولی، شیءگرا و با فیچرهای نسخه‌های مدرن (Java 17 و 21) بیاموزید. این سند، الفبا و اصول مهندسی جاوا را پوشش می‌دهد.

---

## 🎯 اهداف این فاز

- [ ] درک تفاوت Primitive Types و Reference Types (Wrapper Classes).
- [ ] تسلط کامل بر اصول شیءگرایی (OOP) و طراحی بر پایه Interface.
- [ ] یادگیری ویژگی‌های مدرن جاوا: `record`، `sealed classes`، و `pattern matching`.
- [ ] درک مدیریت خطاها و تفاوت Checked vs Unchecked Exceptions.
- [ ] رعایت اصول پنج‌گانه SOLID در نوشتن کدهای تمیز.

---

## 1️⃣ انواع داده و تفاوت Primitive با Objects

در جاوا دو دسته متغیر داریم:

1. **انواع اولیه (Primitives):** مثل `int`, `long`, `double`, `boolean`, `char`. این متغیرها مستقیماً مقدار را در خانه حافظه نگه می‌دارند، در `Stack` ذخیره می‌شوند و سریع هستند (نمی‌توانند `null` باشند).
2. **انواع ارجاعی (Reference Types):** شیءهایی هستند که در `Heap` تخصیص می‌یابند و متغیر موجود در استک فقط یک آدرس (Reference) به آنها دارد؛ مثل `String`, `Integer`, `List`.

> ⚠️ **نکته کلیدی برای پرفورمنس:** از Autoboxing ناخواسته در حلقه‌های سنگین پرهیز کنید. تبدیل مکرر `int` به `Integer` سربار ساخت شیء در هیپ ایجاد کرده و Garbage Collector را به زحمت می‌اندازد!

---

## 2️⃣ ویژگی‌های مدرن در Java 17 و 21

### الف) رکوردهای جاوا (`record`) برای DTOها و داده‌های تغییرناپذیر (Immutable)

به جای نوشتن کلاس‌های طولانی با انبوهی از Getter, Setter, toString, equals و hashCode، در جاوا مدرن از Record استفاده می‌کنیم:

```java
// تعریف یک DTO امن و تغییرناپذیر تنها در یک خط!
public record UserResponseDto(
    Long id,
    String username,
    String email,
    boolean isActive
) {
    // اعتبارسنجی جمع‌وجور (Compact Constructor)
    public UserResponseDto {
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("ایمیل نامعتبر است!");
        }
    }
}
```

### ب) پترن مچینگ برای switch (Pattern Matching for Switch)

به جای کدهای طولانی `if (obj instanceof String) { String s = (String) obj; ... }`:

```java
public String formatNotification(Object event) {
    return switch (event) {
        case UserRegisteredEvent u -> "کاربر جدید ثبت نام کرد: " + u.username();
        case OrderPlacedEvent o    -> "سفارش جدید با مبلغ: " + o.totalAmount();
        case null                  -> "رویداد تهی دریافت شد!";
        default                    -> "رویداد ناشناخته";
    };
}
```

### ج) کلاس‌های مهر و موم شده (`sealed classes`)

محدود کردن وراثت به کلاس‌های مشخص جهت مدل‌سازی دامنه‌های تجاری:

```java
public sealed interface PaymentMethod permits CreditCard, BankTransfer, CryptoWallet {
    void process(BigDecimal amount);
}

public final class CreditCard implements PaymentMethod {
    @Override public void process(BigDecimal amount) { /* پردازش کارت */ }
}
public final class BankTransfer implements PaymentMethod {
    @Override public void process(BigDecimal amount) { /* واریز پایا */ }
}
public final class CryptoWallet implements PaymentMethod {
    @Override public void process(BigDecimal amount) { /* انتقال رمزارز */ }
}
```

---

## 3️⃣ اصول پنج‌گانه SOLID در جاوا

1. **S - Single Responsibility Principle (تک مسئولیتی):** هر کلاس باید تنها یک دلیل برای تغییر داشته باشد (مثلاً کلاس لاجیک محاسباتی نباید کار ارسال ایمیل یا ساخت PDF را انجام دهد).
2. **O - Open/Closed Principle (باز/بسته):** نرم‌افزار باید برای توسعه باز، اما برای تغییر بسته باشد (استفاده از Interface و Polymorphism).
3. **L - Liskov Substitution Principle (جایگزینی لیسکوف):** زیرکلاس‌ها باید بتوانند بدون ایجاد خطا جایگزین کلاس‌های والد شوند.
4. **I - Interface Segregation Principle (جداسازی رابط‌ها):** ترجیح دادن چند Interface کوچک و متمرکز به یک Interface بزرگ و شلوغ.
5. **D - Dependency Inversion Principle (وارونگی وابستگی):** ماژول‌های سطح بالا نباید به ماژول‌های سطح پایین وابسته باشند؛ هر دو باید به انتزاع‌ها (Interfaces) وابسته باشند (اساس کار Spring Framework).

---

## 4️⃣ مدیریت استثناها (Exceptions)

در جاوا استثناها به دو دسته تقسیم می‌شوند:

- **Checked Exceptions (فرزندان** `Exception` **به جز** `RuntimeException`**):** کامپایلر شما را مجبور به `try-catch` یا `throws` می‌کند (مثل `IOException`, `SQLException`).
- **Unchecked Exceptions (فرزندان** `RuntimeException`**):** خطاهای منطقی و برنامه‌نویسی هستند و کامپایلر اجباری نمی‌گذارد (مثل `NullPointerException`, `IllegalArgumentException`).

> 💡 **بهترین شیوه در بک‌اند:** در اپلیکیشن‌های مدرن اسپرینگ، بیشتر از **Unchecked Custom Exceptions** ارث‌بری می‌کنیم (مثلاً `ResourceNotFoundException`) تا کد با `try-catch`های مکرر کثیف نشود و در کنترلر متمرکز مدیریت گردد.

---

## ❓ سوالات متداول مصاحبه‌های فنی این بخش

1. **تفاوت** `String`**،** `StringBuilder` **و** `StringBuffer` **چیست؟ چرا String تغییرناپذیر (Immutable) است؟**
   - *پاسخ خلاصه:* استرینگ برای امنیت، Caching در String Pool و Thread-safety ایموتابل است. برای تغییرات مکرر متنی در یک ترد از `StringBuilder` (بدون سنکرونایز و بسیار سریع) و در محیط چندتردی از `StringBuffer` استفاده می‌شود.
2. **چرا نباید از** `equals` **پیش‌فرض کلاس** `Object` **برای اشیاء بدون override کردن استفاده کنیم؟**
   - *پاسخ خلاصه:* متد `equals` در کلاس `Object` آدرس حافظه (Reference equality) را مقایسه می‌کند، نه برابری معنایی مقادیر را.
