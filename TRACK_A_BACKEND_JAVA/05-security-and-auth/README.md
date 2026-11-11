# 🛡️ فاز ۵: امنیت و احراز هویت (Spring Security 6 & JWT)

امنیت نرم‌افزار شوخی‌بردار نیست. در این ماژول، نحوه برقراری امنیت بی‌نقص در Spring Boot 3 را با استفاده از فیلترچِین Spring Security 6 و توکن‌های JWT (JSON Web Tokens) به‌صورت عملی یاد می‌گیرید.

---

## 🎯 اهداف این فاز
- [ ] درک تفاوت مفهوم احراز هویت (Authentication) و کنترل دسترسی (Authorization).
- [ ] یادگیری چرخه جریان درخواست در زنجیره فیلترهای امنیتی (`SecurityFilterChain`).
- [ ] پیاده‌سازی مکانیزم احراز هویت بدون وضعیت (Stateless Auth) با JWT.
- [ ] پیاده‌سازی توکن تازه‌سازی (Refresh Token) برای تمدید امن جلسه کاربر.
- [ ] هش کردن امن گذرواژه‌ها با الگوریتم استاندارد BCrypt.
- [ ] اعمال محدودیت‌های دسترسی بر اساس نقش کاربران (RBAC) با `@PreAuthorize`.

---

## 1️⃣ معماری Spring Security 6 (مبتنی بر Bean)

در اسپرینگ سکوریتی ۶ دیگر خبری از ارث‌بری از `WebSecurityConfigurerAdapter` نیست. همه چیز با ساخت یک Bean از نوع `SecurityFilterChain` کانفیگ می‌شود:

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity // فعال‌سازی @PreAuthorize روی متدهای سرویس و کنترلر
public class SecurityConfig {

    private final JwtAuthenticationFilter jwtAuthFilter;
    private final UserDetailsService userDetailsService;

    public SecurityConfig(JwtAuthenticationFilter jwtAuthFilter, UserDetailsService userDetailsService) {
        this.jwtAuthFilter = jwtAuthFilter;
        this.userDetailsService = userDetailsService;
    }

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        return http
            .csrf(AbstractHttpConfigurer::disable) // غیرفعال کردن CSRF برای REST APIهای بدون State
            .sessionManagement(session -> session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/v1/auth/**", "/swagger-ui/**", "/v3/api-docs/**").permitAll()
                .requestMatchers("/api/v1/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .authenticationProvider(authenticationProvider())
            .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class)
            .build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder(12); // ضریب کارایی ۱۲ برای مقاومت در برابر Brute-force
    }
}
```

---

## 2️⃣ دیاگرام جریان احراز هویت با JWT

```
1. درخواست لاگین: POST /api/v1/auth/login { "email", "password" }
   │
   ▼
[ کنترلر و سرویس Auth ]
   │ 1. چک کردن پسورد با BCryptPasswordEncoder
   │ 2. ساخت Access Token (عمر ۱۵ دقیقه) + Refresh Token (عمر ۷ روز در دیتابیس)
   ▼
کلاینت توکن‌ها را در حافظه امن ذخیره می‌کند.

2. درخواست‌های بعدی به اندپوینت‌های محافظت‌شده: GET /api/v1/orders
   Header: Authorization: Bearer <jwt_access_token>
   │
   ▼
[ JwtAuthenticationFilter ]
   ├── ۱. بررسی امضا (Signature) و تاریخ انقضا (Expiration)
   ├── ۲. استخراج Username و Roles از درون Payload توکن
   └── ۳. ثبت هویت در SecurityContextHolder
   │
   ▼
[ کنترلر پاسخ را بازمی‌گرداند ]
```

---

## 3️⃣ پیاده‌سازی فیلتر اختصاصی `JwtAuthenticationFilter`

```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    private final JwtService jwtService;
    private final UserDetailsService userDetailsService;

    public JwtAuthenticationFilter(JwtService jwtService, UserDetailsService userDetailsService) {
        this.jwtService = jwtService;
        this.userDetailsService = userDetailsService;
    }

    @Override
    protected void doFilterInternal(
        @NonNull HttpServletRequest request,
        @NonNull HttpServletResponse response,
        @NonNull FilterChain filterChain
    ) throws ServletException, IOException {

        final String authHeader = request.getHeader("Authorization");
        if (authHeader == null || !authHeader.startsWith("Bearer ")) {
            filterChain.doFilter(request, response);
            return;
        }

        final String jwt = authHeader.substring(7);
        final String userEmail = jwtService.extractUsername(jwt);

        if (userEmail != null && SecurityContextHolder.getContext().getAuthentication() == null) {
            UserDetails userDetails = this.userDetailsService.loadUserByUsername(userEmail);

            if (jwtService.isTokenValid(jwt, userDetails)) {
                UsernamePasswordAuthenticationToken authToken = new UsernamePasswordAuthenticationToken(
                    userDetails,
                    null,
                    userDetails.getAuthorities()
                );
                authToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                SecurityContextHolder.getContext().setAuthentication(authToken);
            }
        }
        filterChain.doFilter(request, response);
    }
}
```

---

## 4️⃣ کنترل دسترسی بر اساس نقش (RBAC)

با استفاده از انوتیشن `@PreAuthorize` می‌توانید کنترل دقیق روی سطح کلاس یا تک‌تک متدها داشته باشید:

```java
@RestController
@RequestMapping("/api/v1/admin/users")
@PreAuthorize("hasRole('ADMIN')") // تمام متدهای این کنترلر فقط برای ادمین در دسترس است
public class AdminUserController {

    @DeleteMapping("/{id}")
    @PreAuthorize("hasAuthority('users:delete')")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.deleteUser(id);
        return ResponseEntity.noContent().build();
    }
}
```

---

## ❓ سوالات متداول مصاحبه‌های فنی این بخش
1. **چرا برای REST APIهای مبتنی بر JWT باید CSRF را غیرفعال کرد؟**
   - *پاسخ:* حمله CSRF متکی بر ارسال اتوماتیک کوکی‌های مرورگر در درخواست‌های مخفیانه است. چون توکن‌های JWT در هدر `Authorization: Bearer` ارسال می‌شوند و مرورگرها هرگز به صورت خودکار هدر سفارشی را الصاق نمی‌کنند، این نوع APIها ذاتاً در برابر CSRF مقاوم هستند.
2. **چگونه می‌توان یک توکن JWT نامعتبر را پیش از موعد انقضا باطل (Revoke) کرد؟**
   - *پاسخ:* چون JWT سلف‌کانتین و بدون استیت است، برای ابطال فوری آن (مثلاً در زمان خروج یا تغییر پسورد)، از یک **Blacklist در ردیس با TTL برابر زمان باقی‌مانده توکن** استفاده می‌شود تا فیلتر ابتدا بررسی کند آیا توکن در بلک‌لیست است یا خیر.
