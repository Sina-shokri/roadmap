# ⚡ فاز ۵: هم‌زمانی، چندنخی و ترد‌های مجازی (Concurrency & Virtual Threads)

سرورهای وب جاوا (مثل Tomcat در اسپرینگ بوت) ذاتا چندنخی (Multithreaded) هستند؛ یعنی به ازای هر درخواست کاربر یک Thread اجرا می‌شود. اگر ندانید چگونه منابع مشترک را مدیریت کنید، با خطاهای عجیبی مثل فروش هم‌زمان یک بلیت به دو مسافر، یا بن‌بست سرور مواجه خواهید شد.

---

## 🎯 اهداف این فاز
- [ ] درک تفاوت پردازه (Process) و نخ (Thread) در سطح سیستم‌عامل.
- [ ] شناخت شرایط رقابتی (Race Condition) و ناحیه بحرانی (Critical Section).
- [ ] درک مدل حافظه جاوا (JMM)، کلمه کلیدی `volatile` و متغیرهای اتمیک (`AtomicInteger`).
- [ ] تفاوت قفل ذاتی (`synchronized`) با قفل‌های پیشرفته (`ReentrantLock`).
- [ ] مدیریت بهینه استخر نخ‌ها (Thread Pools) برای کارهای CPU-bound و I/O-bound.
- [ ] **انقلاب بزرگ Virtual Threads در Java 21** و چرایی دگرگون شدن مقیاس‌پذیری جاوا.
- [ ] شناخت بن‌بست (Deadlock) و راه‌های کشف و جلوگیری از آن.

---

## 1️⃣ پدیده Race Condition در عمل

فرض کنید دو کاربر هم‌زمان متد زیر را صدا می‌زنند:

```java
// کد پر از باگ به دلیل عدم هم‌زمانی امن (Thread Unsafe)
public class BankAccount {
    private int balance = 100;

    public void withdraw(int amount) {
        if (balance >= amount) {           // گام ۱: خواندن موجودی
            try { Thread.sleep(50); } catch (InterruptedException e) {}
            balance = balance - amount;    // گام ۲: کسر موجودی
        }
    }
}
```
اگر دو کاربر به طور هم‌زمان متد `withdraw(100)` را صدا بزنند، هر دو شرط `balance >= 100` را درست می‌بینند و هر دو ۱۰۰ تومان کسر می‌کنند؛ موجودی به $100 - 200 = -100$ می‌رسد و حساب منفی می‌شود!

### راه‌حل‌ها:
1. **استفاده از `synchronized`:**
   ```java
   public synchronized void withdraw(int amount) {
       if (balance >= amount) {
           balance -= amount;
       }
   }
   ```
2. **استفاده از عملیات اتمیک بدون قفل (Lock-free با CAS):**
   ```java
   private final AtomicInteger balance = new AtomicInteger(100);

   public boolean withdraw(int amount) {
       return balance.updateAndGet(current -> current >= amount ? current - amount : current) >= 0;
   }
   ```

---

## 2️⃣ مدل حافظه جاوا (JMM) و نقش `volatile`

پردازنده‌های مدرن برای سرعت بالا دارای رجیسترها و L1/L2 Cache اختصاصی در هر هسته هستند. اگر یک ترد متغیری را تغییر دهد، ممکن است هسته دیگر مقدار جدید را در رم نبیند (مشکل قابلیت دید - Visibility Problem):

```java
public class Worker implements Runnable {
    // کلمه volatile تضمین می‌کند تغییرات این متغیر بلافاصله در حافظه رم اصلی نوشته و خوانده شود
    private volatile boolean running = true;

    public void stopRunning() {
        this.running = false;
    }

    @Override
    public void run() {
        while (running) {
            // انجام کار
        }
    }
}
```

---

## 3️⃣ انقلاب ترد‌های مجازی (Virtual Threads) در Java 21

تا پیش از جاوا ۲۱، هر ترد در جاوا یک نگاشت ۱ به ۱ با **نخ سیستم‌عامل (OS Platform Thread)** داشت:
- هر نخ سیستم‌عامل حدود **۱ مگابایت حافظه Stack** مصرف می‌کرد.
- ساخت بیش از چند هزار نخ باعث کرش کردن سرور با خطای `OutOfMemoryError: unable to create native thread` می‌شد.
- وقتی ترد منتظر دیتابیس یا وب‌سرویس دیگر می‌شد (I/O Blocking)، هسته پردازنده معطل می‌ماند.

### ترد مجازی چگونه بازی را عوض کرد؟
در جاوا ۲۱ (پروژه Loom)، ترد‌های مجازی بسیار سبک (فقط چند صد بایت!) در سطح JVM مدیریت می‌شوند:

```
[ میلیون‌ها Virtual Thread ]
          │
          │ به صورت M:N نگاشت می‌شوند روی:
          ▼
[ تعداد کمی Carrier Thread در سطح سیستم‌عامل (مثلاً ۸ تا به تعداد هسته‌های CPU) ]
```

هر زمان یک Virtual Thread منتظر دیتابیس بماند (I/O Blocking)، JVM فوراً آن را از روی Carrier Thread برمی‌دارد و ترد دیگری را روی پردازنده قرار می‌دهد!

### چگونه در Spring Boot 3.2+ فعالش کنیم؟
تنها با اضافه کردن یک خط در `application.yml`:
```yaml
spring:
  threads:
    virtual:
      enabled: true
```
با همین یک تنظیم ساده، سرور تامکت می‌تواند به جای ۲۰۰ ریکوئست هم‌زمان، ده‌ها هزار درخواست هم‌زمان ورودی را بدون افت سرعت پاسخ دهد!

---

## 4️⃣ استخر نخ‌ها (Thread Pools) و تنظیم اندازه

اگر عملیات شما پردازشی و فشرده از نظر CPU است (مثل رمزگذاری، فشرده‌سازی تصویر):
$$\text{Pool Size} = N_{\text{CPU}} + 1$$

اگر عملیات منتظر دیتابیس و شبکه است (I/O Bound):
$$\text{Pool Size} = N_{\text{CPU}} \times \left(1 + \frac{\text{Wait Time}}{\text{Compute Time}}\right)$$

```java
// ساخت تردپول استاندارد در اسپرینگ
@Bean
public Executor customTaskExecutor() {
    ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
    executor.setCorePoolSize(10);
    executor.setMaxPoolSize(50);
    executor.setQueueCapacity(500);
    executor.setThreadNamePrefix("AsyncWorker-");
    executor.initialize();
    return executor;
}
```

---

## 5️⃣ ۴ شرط ایجاد بن‌بست (Deadlock) و پیشگیری

بن‌بست زمانی رخ می‌دهد که ترد ۱ قفل A را دارد و منتظر قفل B است، در حالی که ترد ۲ قفل B را دارد و منتظر قفل A است.

**شرایط ۴ گانه کافی برای بن‌بست (Coffman Conditions):**
1. **Mutual Exclusion:** منابع غیرقابل اشتراک هستند.
2. **Hold and Wait:** تردی منبعی را نگه داشته و منتظر منبع دیگری است.
3. **No Preemption:** نمی‌توان به اجبار منبع را از ترد گرفت.
4. **Circular Wait:** چرخه‌ای از انتظار بین تردها وجود دارد.

> 🛡️ **راه‌حل طلایی مهندسی:** همیشه تمام قفل‌ها را در کل سیستم با **ترتیب یکسان (Lock Ordering)** بگیرید! (مثلاً همیشه ابتدا حساب با شناسه کوچکتر قفل شود، سپس حساب با شناسه بزرگتر).
