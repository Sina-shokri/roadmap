# 🚢 فاز ۶: تست‌نویسی، کانتینرسازی و آمادگی پروداکشن (Testing & Production)

کدی که تست نشده باشد در محیط پروداکشن کار نخواهد کرد! تفاوت یک توسعه‌دهنده تازه‌کار با یک مهندس ارشد بک‌اند، در تسلط بر تست‌نویسی خودکار، داکر و قابلیت رصد وضعیت (Observability) در پروداکشن است.

---

## 🎯 اهداف این فاز
- [ ] هرم تست نرم‌افزار (Testing Pyramid) و انواع تست در اسپرینگ.
- [ ] تست واحد لایه سرویس با JUnit 5 و فریم‌ورک Mockito.
- [ ] تست لایه کنترلر با `@WebMvcTest` و MockMvc.
- [ ] تست یکپارچگی پیشرفته با دیتابیس واقعی به کمک **Testcontainers**.
- [ ] نوشتن Dockerfile بهینه چندمرحله‌ای (Multi-stage Build).
- [ ] مانیتورینگ سلامت سرویس با Spring Boot Actuator.

---

## 1️⃣ تست واحد لایه سرویس (Unit Testing با Mockito)

در تست واحد، ما تمام وابستگی‌های خارجی (مانند دیتابیس یا وب‌سرویس‌های دیگر) را شبیه‌سازی (Mock) می‌کنیم تا سرعت اجرای تست‌ها در حد میلی‌ثانیه باشد:

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    @Mock
    private PasswordEncoder passwordEncoder;

    @InjectMocks
    private UserService userService;

    @Test
    @DisplayName("ثبت‌نام کاربر با ایمیل تکراری باید خطا پرتاب کند")
    void registerUser_WhenEmailAlreadyExists_ShouldThrowException() {
        // Given
        RegisterRequest request = new RegisterRequest("Amir", "test@example.com", "pass123");
        when(userRepository.existsByEmail("test@example.com")).thenReturn(true);

        // When & Then
        assertThrows(DuplicateResourceException.class, () -> userService.register(request));
        
        // اطمینان از اینکه متد ذخیره هرگز صدا زده نشده است
        verify(userRepository, never()).save(any(User.class));
    }
}
```

---

## 2️⃣ تست یکپارچگی با پایگاه‌داده واقعی (Testcontainers)

بزرگترین اشتباه در تست اسپرینگ، استفاده از H2 Database در تست‌ها است؛ زیرا رفتار H2 با PostgreSQL تفاوت‌های زیادی در ایندکس‌ها، کوئری‌ها و JSONB دارد! کتابخانه **Testcontainers** یک کانتینر داکر واقعی PostgreSQL را هنگام اجرای تست بالا می‌آورد:

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@Testcontainers
class OrderIntegrationTest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16-alpine");

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }

    @Autowired
    private OrderRepository orderRepository;

    @Test
    void shouldPersistAndRetrieveOrderCorrectly() {
        Order order = new Order("ORD-1001", OrderStatus.PENDING);
        Order saved = orderRepository.save(order);

        Optional<Order> fetched = orderRepository.findById(saved.getId());
        assertTrue(fetched.isPresent());
        assertEquals("ORD-1001", fetched.get().getOrderNumber());
    }
}
```

---

## 3️⃣ ساخت Dockerfile چندمرحله‌ای (Multi-stage Build)

یک ایمیج داکر برای پروداکشن باید سبک، امن و بدون ابزارهای اضافی بیلد باشد:

```dockerfile
# گام ۱: بیلد کردن پروژه
FROM maven:3.9.6-eclipse-temurin-21-alpine AS builder
WORKDIR /app
COPY pom.xml .
# دانلود وابستگی‌ها برای استفاده از لایه‌های کَش داکر
RUN mvn dependency:go-offline -B
COPY src ./src
RUN mvn clean package -DskipTests

# گام ۲: ساخت ایمیج نهایی بسیار سبک فقط با JRE
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app

# ایجاد کاربر غیر روت برای امنیت پروداکشن
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

COPY --from=builder /app/target/*.jar app.jar

EXPOSE 8080

# تنظیم پرچم‌های JVM برای استفاده بهینه از حافظه کانتینر
ENTRYPOINT ["java", "-XX:+UseContainerSupport", "-XX:MaxRAMPercentage=75.0", "-jar", "app.jar"]
```

---

## 4️⃣ مانیتورینگ سلامت با Spring Boot Actuator

با اضافه کردن `spring-boot-starter-actuator`، مسیرهای پایش سلامت سیستم فعال می‌شوند:

```yaml
# application.yml
management:
  endpoints:
    web:
      exposure:
        include: "health,info,metrics,prometheus"
  endpoint:
    health:
      show-details: when_authorized
```
اندپوینت `/actuator/health` به سیستم‌های مانند Kubernetes، Docker Swarm یا ابزارهای مانیتورینگ اطلاع می‌دهد که آیا اپلیکیشن سالم است و اتصال به دیتابیس و ردیس برقرار است یا خیر.
