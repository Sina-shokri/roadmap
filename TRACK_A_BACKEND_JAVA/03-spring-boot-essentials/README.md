# 🍃 فاز ۳: مبانی و هسته اسپرینگ بوت ۳ (Spring Boot 3 Essentials)

اسپرینگ محبوب‌ترین فریم‌ورک جاوا در سراسر جهان برای شرکت‌های اینترنتی و بانکی است. نسخه Spring Boot 3 بر پایه **Java 17/21** و **Spring Framework 6** ساخته شده و استاندارد جدید دنیای بک‌اند است.

---

## 🎯 اهداف این فاز
- [ ] درک عمیق مفهوم Inversion of Control (IoC) و Dependency Injection (DI).
- [ ] شناخت چرخه حیات Beanها و اسکوپ‌های آنها (`Singleton` در برابر `Prototype`).
- [ ] ساختار لایه‌ای استاندارد وب‌سرویس (Controller $\to$ Service $\to$ Repository).
- [ ] طراحی REST API استاندارد مطابق با کدهای وضعیت HTTP.
- [ ] اعتبارسنجی ورودی‌ها با Bean Validation و مدیریت استثناها به صورت سراسری با `@RestControllerAdvice`.
- [ ] تولید اتوماتیک داکیومنت API با Swagger / OpenAPI.

---

## 1️⃣ قلب تپنده اسپرینگ: IoC و تزریق وابستگی (DI)

در برنامه‌نویسی سنتی، هر کلاس خودش اشیاء وابسته‌اش را با `new` می‌سازد. در اسپرینگ، مدیریت ساخت اشیاء (Beans) و تزریق آنها به یکدیگر به فریم‌ورک واگذار می‌شود:

```
[ IoC Container (ApplicationContext) ]
  ├── UserService Bean (Singleton)
  ├── UserRepository Bean (Singleton)
  └── EmailService Bean (Singleton)
             │
             ▼ تزریق اتوماتیک به یکدیگر بر اساس نوع
```

### بهترین روش تزریق وابستگی: Constructor Injection
تزریق وابستگی از طریق فیلد با `@Autowired` دیگر توصیه نمی‌شود! همیشه از تزریق از طریق Constructor استفاده کنید تا وابستگی‌ها `final` و تست‌پذیر باشند:

```java
@Service
public class OrderService {

    private final OrderRepository orderRepository;
    private final PaymentGateway paymentGateway;

    // Constructor Injection (در اسپرینگ 4.3+ نیازی به نوشتن @Autowired روی سازنده تک نیست)
    public OrderService(OrderRepository orderRepository, PaymentGateway paymentGateway) {
        this.orderRepository = orderRepository;
        this.paymentGateway = paymentGateway;
    }

    public Order createOrder(CreateOrderRequest request) {
        // منطق کسب‌وکار
        return orderRepository.save(new Order(...));
    }
}
```

---

## 2️⃣ معماری چند لایه (Layered Architecture)

یک وب‌سرویس حرفه‌ای اسپرینگ بوت باید به صورت واضح تفکیک مسئولیت داشته باشد:

```
[ کلاینت (فرانت‌اند / موبایل) ]
            │ HTTP Request (JSON)
            ▼
┌──────────────────────────────┐
│       1. Web Controller      │  👈 فقط دریافت درخواست، ولیدیشن و بازگرداندن پاسخ
├──────────────────────────────┤
│       2. Service Layer       │  👈 قلب منطق تجاری (Business Logic) و تراکنش‌ها
├──────────────────────────────┤
│    3. Repository (Data)      │  👈 ارتباط مستقیم با پایگاه‌داده
└──────────────────────────────┘
            │ SQL
            ▼
     [ پایگاه‌داده ]
```

---

## 3️⃣ ساخت REST Controller استاندارد

```java
@RestController
@RequestMapping("/api/v1/tasks")
@Tag(name = "Tasks", description = "مدیریت تسک‌ها و پروژه‌ها")
public class TaskController {

    private final TaskService taskService;

    public TaskController(TaskService taskService) {
        this.taskService = taskService;
    }

    @PostMapping
    public ResponseEntity<TaskResponseDto> createTask(@Valid @RequestBody CreateTaskRequest request) {
        TaskResponseDto created = taskService.createTask(request);
        // کد ۲۰۰ یا ۲۰۱ (Created) به همراه هدر مکان
        URI location = URI.create("/api/v1/tasks/" + created.id());
        return ResponseEntity.created(location).body(created);
    }

    @GetMapping("/{id}")
    public ResponseEntity<TaskResponseDto> getTaskById(@PathVariable Long id) {
        return ResponseEntity.ok(taskService.getTaskById(id));
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteTask(@PathVariable Long id) {
        taskService.deleteTask(id);
        return ResponseEntity.noContent().build(); // کد ۲04
    }
}
```

---

## 4️⃣ اعتبارسنجی ورودی‌ها (Validation)

با اضافه کردن `spring-boot-starter-validation`، کنترل داده‌های ورودی قبل از رسیدن به منطق بیزینس انجام می‌شود:

```java
public record CreateTaskRequest(
    @NotBlank(message = "عنوان تسک نباید خالی باشد")
    @Size(min = 3, max = 100, message = "عنوان تسک باید بین ۳ تا ۱۰۰ کاراکتر باشد")
    String title,

    @Size(max = 500, message = "توضیحات نباید بیش از ۵۰۰ کاراکتر باشد")
    String description,

    @NotNull(message = "اولویت تسک باید مشخص شود")
    Priority priority,

    @FutureOrPresent(message = "مهلت تسک نمی‌تواند در گذشته باشد")
    LocalDate dueDate
) {}
```

---

## 5️⃣ مدیریت متمرکز خطاها (Global Exception Handling)

در صورت بروز هر خطا، باید پاسخی استاندارد با فرمت RFC 7807 (Problem Details) به کلاینت برگردانیم:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ProblemDetail handleNotFound(ResourceNotFoundException ex) {
        ProblemDetail problem = ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage());
        problem.setTitle("منبع مورد نظر پیدا نشد");
        problem.setProperty("timestamp", Instant.now());
        return problem;
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ProblemDetail handleValidationErrors(MethodArgumentNotValidException ex) {
        ProblemDetail problem = ProblemDetail.forStatusAndDetail(HttpStatus.BAD_REQUEST, "خطای اعتبارسنجی مقادیر ارسالی");
        Map<String, String> errors = new HashMap<>();
        for (FieldError fieldError : ex.getBindingResult().getFieldErrors()) {
            errors.put(fieldError.getField(), fieldError.getDefaultMessage());
        }
        problem.setProperty("validationErrors", errors);
        return problem;
    }
}
```

---

## ❓ سوالات متداول مصاحبه‌های فنی این بخش
1. **تفاوت `@Component`، `@Service` و `@Repository` چیست؟**
   - *پاسخ:* از نظر فنی همگی Bean اسپرینگ می‌سازند، اما `@Service` برای لایه تجاری و معنایی کد است و `@Repository` خطاهای دسترسی به دیتابیس (SQL) را به صورت اتوماتیک به `DataAccessException`های اسپرینگ ترجمه می‌کند.
2. **چرا نباید Entityهای دیتابیس مستقیماً در خروجی Controller به کلاینت بازگردانده شوند؟**
   - *پاسخ:* این کار باعث نشت اطلاعات حساس دیتابیس، وابسته شدن فرانت‌اند به ساختار جداول و ایجاد خطاهای وحشتناک `LazyInitializationException` هنگام سریالایز شدن فیلدهای لود تنبل می‌شود. همیشه باید از DTO استفاده شود!
