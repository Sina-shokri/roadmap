# 📦 فاز ۲: کالکشن‌ها، استریم‌ها و برنامه‌نویسی تابعی (Collections & Streams)

بخش بزرگی از کار یک برنامه‌نویس بک‌اند در جاوا، کار با لیست‌ها، دسته‌بندی داده‌ها، جستجو، تبدیل فرمت‌ها و فیلتر کردن اطلاعات است. تسلط بر Collections Framework و Streams API شما را از نوشتن کدهای طولانی و پرباگ بی‌نیاز می‌کند.

---

## 🎯 اهداف این فاز
- [ ] تسلط بر ساختار اینترفیس‌های کالکشن: `List`, `Set`, `Queue`, `Map`.
- [ ] یادگیری تفاوت‌های عمیق `ArrayList` و `LinkedList` و سناریوهای کاربرد هرکدام.
- [ ] درک چرایی و چگونگی قرارداد حیاتی `equals()` و `hashCode()`.
- [ ] تسلط بر اینترفیس‌های تابعی اصلی (`Predicate`, `Function`, `Consumer`, `Supplier`).
- [ ] پردازش تمیز و حرفه‌ای دیتا با Streams API (`filter`, `map`, `flatMap`, `groupingBy`).
- [ ] حذف کامل `NullPointerException` با یادگیری الگوی `Optional`.

---

## 1️⃣ درخت سلسله‌مراتب Collections در جاوا

```
             Iterable
                │
            Collection
     ┌──────────┼──────────┐
    List       Set       Queue / Deque
     │          │          │
┌────┴───┐ ┌────┴────┐ ┌───┴───┐
ArrayList  HashSet   PriorityQueue
LinkedList TreeSet   ArrayDeque

(نکته: Map در اینترفیس Collection ارث‌بری نمی‌کند و ساختار کلید-مقداری مستقل دارد)
                 Map
          ┌───────┴───────┐
       HashMap         TreeMap
     LinkedHashMap  ConcurrentHashMap
```

### مقایسه کالکشن‌های رایج:
| ساختار داده | ترتیب عناصر (Order) | داده تکراری (Duplicates) | پیچیدگی دسترسی (Search/Get) | کاربرد معمول |
| :--- | :--- | :--- | :--- | :--- |
| **`ArrayList`** | حفظ ترتیب درج (Index-based) | بله | $O(1)$ بر اساس ایندکس | پیش‌فرض برای ۹۹٪ لیست‌ها |
| **`LinkedList`** | حفظ ترتیب پیوندی | بله | $O(n)$ چون باید گره‌ها پیمایش شوند | زمانی که درج/حذف مکرر در ابتدا یا وسط داریم |
| **`HashSet`** | نامشخص | خیر | $O(1)$ میانگین | نگهداری مقادیر یونیک بدون اهمیت ترتیب |
| **`TreeSet`** | مرتب‌شده بر اساس مقایسه (Natural / Comparator) | خیر | $O(\log n)$ (Red-Black Tree) | نیاز به داده‌های سورت‌شده دائمی |
| **`HashMap`** | نامشخص | کلید نامکرر، مقدار مکرر | $O(1)$ میانگین | اتصال کلید به مقدار (Cache، رجیستری) |
| **`LinkedHashMap`**| حفظ ترتیب درج | کلید نامکرر، مقدار مکرر | $O(1)$ | ساخت سیستم‌های Cache مبتنی بر LRU |

---

## 2️⃣ قرارداد حیاتی `equals()` و `hashCode()`

اگر کلاسی را می‌سازید که قرار است کلید یک `HashMap` باشد یا در `HashSet` قرار گیرد، **حتماً** باید این دو متد را با هم بازنویسی (Override) کنید:
1. **اگر `a.equals(b)` مقدار `true` بدهد، حتماً باید `a.hashCode() == b.hashCode()` باشد.**
2. برعکس آن الزامی نیست (ممکن است دو شیء متفاوت هش‌کد یکسان تولید کنند که به آن Hash Collision می‌گویند).

```java
public class Product {
    private final String sku;
    private final String name;

    public Product(String sku, String name) {
        this.sku = sku;
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Product product = (Product) o;
        return Objects.equals(sku, product.sku);
    }

    @Override
    public int hashCode() {
        return Objects.hash(sku);
    }
}
```
> 💡 *نکته جذاب جاوا ۲۱:* اگر از `record` استفاده کنید، کامپایلر جاوا به صورت اتوماتیک متدهای `equals` و `hashCode` کاملاً دقیق و امن را برایتان می‌سازد!

---

## 3️⃣ قدرت Streams API در دنیای واقعی

استریم‌ها اجازه می‌دهند داده‌ها را به شکل خط تولید پردازش کنیم (بدون تغییر دادن دیتای اولیه - Immutability):

```java
// سناریو: استخراج ایمیل کاربران فعال که بالای ۵ خرید داشته‌اند، به ترتیب حروف الفبا
List<String> activeVipEmails = users.stream()
    .filter(User::isActive)                          // فیلتر کردن
    .filter(u -> u.getOrderCount() >= 5)             // شرط تعداد سفارش
    .map(User::getEmail)                             // تبدیل User به String (ایمیل)
    .map(String::toLowerCase)                        // نرمال‌سازی
    .distinct()                                      // حذف ایمیل‌های تکراری
    .sorted()                                        // مرتب‌سازی
    .toList();                                       // در جاوا 16+ به جای .collect(Collectors.toList())
```

### عملیات پیشرفته دسته‌بندی با `groupingBy`:
```java
// دسته‌بندی سفارش‌ها بر اساس وضعیت (PENDING, PAID, CANCELLED)
Map<OrderStatus, List<Order>> ordersByStatus = orders.stream()
    .collect(Collectors.groupingBy(Order::getStatus));

// محاسبه مجموع مبلغ سفارش‌ها در هر وضعیت
Map<OrderStatus, BigDecimal> revenueByStatus = orders.stream()
    .collect(Collectors.groupingBy(
        Order::getStatus,
        Collectors.reducing(BigDecimal.ZERO, Order::getAmount, BigDecimal::add)
    ));
```

---

## 4️⃣ کار اصولی با `Optional`
دیگر هرگز کدهایی مانند `if (user != null)` را در لایه‌های سرویس تکرار نکنید:

```java
// خواندن کاربر از دیتابیس یا پرتاب خطا در صورت پیدا نشدن
User user = userRepository.findByEmail(email)
    .orElseThrow(() -> new ResourceNotFoundException("کاربری با این ایمیل یافت نشد: " + email));

// استخراج مقدار با ترانسفورم تمیز
String city = userRepository.findById(id)
    .map(User::getAddress)
    .map(Address::getCity)
    .orElse("نامشخص");
```

---

## ❓ سوالات متداول مصاحبه‌های فنی این بخش
1. **تفاوت `Collection.stream()` و `Collection.parallelStream()` چیست و چه خطراتی دارد؟**
   - *پاسخ:* استریم موازی از ForkJoinPool مشترک JVM برای تقسیم کار بین هسته‌های پردازنده استفاده می‌کند. برای تسک‌های سبک یا عملیات I/O-bound (ارتباط با دیتابیس یا وب) نباید استفاده شود چون می‌تواند تردپول مشترک سرور را قفل کند.
2. **در صورت نقض قرارداد `equals` و `hashCode` چه اتفاقی در `HashMap` می‌افتد؟**
   - *پاسخ:* اگر دو شیء برابر هش‌کد متفاوت داشته باشند، در دو باکت متفاوت قرار می‌گیرند. در نتیجه، هنگام فراخوانی `map.get(key)`، جاوا به باکت اشتباه رفته و شیء پیدا نمی‌شود، حتی اگر درون مپ وجود داشته باشد!
