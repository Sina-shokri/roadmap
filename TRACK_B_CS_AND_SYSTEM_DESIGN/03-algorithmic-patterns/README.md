# 🧩 فاز ۳: الگوهای الگوریتمی متداول بک‌اند (Algorithmic Patterns)

برای موفقیت در مصاحبه‌های فنی شرکت‌های بزرگ و حل مسائل واقعی سیستم، نیازی به حفظ کردن صدها مسئله نیست؛ کافی است **الگوهای اصلی (Patterns)** را بشناسید.

---

## 🎯 اهداف این فاز
- [ ] تسلط بر الگوی دو اشاره‌گر (Two Pointers).
- [ ] تسلط بر الگوی پنجره لغزان (Sliding Window) و کاربرد آن در محدودسازی نرخ (Rate Limiting).
- [ ] الگوی اشاره‌گرهای سریع و کند (Fast & Slow Pointers).
- [ ] تسلط بر جستجوی دودویی (Binary Search).
- [ ] الگوی Top K Elements با PriorityQueue.
- [ ] پیمایش‌های گراف و درخت (BFS و DFS) برای مدل‌سازی ساختارهای سازمانی و نقش‌ها.

---

## 1️⃣ الگوی دو اشاره‌گر (Two Pointers)

### ایده الگو:
در داده‌های مرتب‌شده، به جای دو حلقه تودرتو ($O(n^2)$)، یک اشاره‌گر را در ابتدا (`left = 0`) و دیگری را در انتها (`right = n - 1`) می‌گذاریم و بر اساس شرایط آنها را به سمت هم حرکت می‌دهیم ($O(n)$).

### مسئله نمونه: مجموع دو عدد برابر با هدف (Two Sum on Sorted Array)
```java
public int[] twoSumSorted(int[] numbers, int target) {
    int left = 0;
    int right = numbers.length - 1;

    while (left < right) {
        int currentSum = numbers[left] + numbers[right];
        if (currentSum == target) {
            return new int[]{left + 1, right + 1};
        } else if (currentSum < target) {
            left++; // جمع کوچک است، عدد بزرگتر می‌خواهیم
        } else {
            right--; // جمع بزرگ است، عدد کوچکتر می‌خواهیم
        }
    }
    return new int[]{-1, -1};
}
```

---

## 2️⃣ الگوی پنجره لغزان (Sliding Window)

### ایده الگو:
برای مسائل مربوط به زیرآرایه (Subarray) یا زیررشته (Substring) پیوسته. پنجره‌ای تعریف می‌کنیم که با افزودن عنصر از سمت راست باز می‌شود و در صورت نقض شرط از سمت چپ جمع می‌شود.

### کاربرد در بک‌اند:
پیاده‌سازی الگوریتم **Sliding Window Rate Limiter** برای کنترل تعداد درخواست‌های کاربر در بازه زمانی مشخص (مثلاً حداکثر ۱۰۰ ریکوئست در هر ۶۰ ثانیه).

### مسئله نمونه: طولانی‌ترین زیررشته بدون کاراکتر تکراری
```java
public int lengthOfLongestSubstring(String s) {
    Map<Character, Integer> charIndexMap = new HashMap<>();
    int maxLength = 0;
    int windowStart = 0;

    for (int windowEnd = 0; windowEnd < s.length(); windowEnd++) {
        char rightChar = s.charAt(windowEnd);

        if (charIndexMap.containsKey(rightChar)) {
            // شروع پنجره را به بعد از کاراکتر تکراری قبلی منتقل می‌کنیم
            windowStart = Math.max(windowStart, charIndexMap.get(rightChar) + 1);
        }

        charIndexMap.put(rightChar, windowEnd);
        maxLength = Math.max(maxLength, windowEnd - windowStart + 1);
    }
    return maxLength;
}
```

---

## 3️⃣ الگوی اشاره‌گر سریع و کند (Tortoise and Hare)

### ایده الگو:
دو اشاره‌گر که یکی با سرعت ۱ گام و دیگری با سرعت ۲ گام در لیست پیوندی حرکت می‌کنند. اگر چرخه‌ای (Cycle) وجود داشته باشد، این دو حتماً به هم می‌رسند.

### کاربرد در بک‌اند:
تشخیص حلقه بی‌نهایت در پردازش گره‌های جریان کاری (Workflow Engines) یا لینک‌های ریدایرکت تو در تو.

```java
public boolean hasCycle(ListNode head) {
    if (head == null) return false;
    ListNode slow = head;
    ListNode fast = head;

    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) {
            return true; // چرخه شناسایی شد!
        }
    }
    return false;
}
```

---

## 4️⃣ الگوی $K$ عنصر برتر (Top K Elements)

### کاربرد در بک‌اند:
یافتن ۱۰ محصول پرفروش لحظه‌ای، رتبه‌بندی کاربران در لیدربورد بازی‌ها، یا پرتکرارترین کلمات جستجو شده.

### راه‌حل بهینه با Min-Heap ($O(n \log k)$ به جای $O(n \log n)$):
```java
public int[] topKFrequent(int[] nums, int k) {
    // ۱. شمارش فرکانس‌ها
    Map<Integer, Integer> frequencyMap = new HashMap<>();
    for (int num : nums) {
        frequencyMap.put(num, frequencyMap.getOrDefault(num, 0) + 1);
    }

    // ۲. Min-Heap برای نگهداری فقط k عنصر برتر
    PriorityQueue<Map.Entry<Integer, Integer>> minHeap = new PriorityQueue<>(
        Comparator.comparingInt(Map.Entry::getValue)
    );

    for (Map.Entry<Integer, Integer> entry : frequencyMap.entrySet()) {
        minHeap.offer(entry);
        if (minHeap.size() > k) {
            minHeap.poll(); // حذف کوچکترین عنصر موجود در هیپ
        }
    }

    // ۳. استخراج خروجی
    int[] result = new int[k];
    for (int i = 0; i < k; i++) {
        result[i] = minHeap.poll().getKey();
    }
    return result;
}
```

---

## 5️⃣ پیمایش گراف و درخت: BFS و DFS

- **Breadth-First Search (BFS):** با استفاده از `Queue` لایه به لایه حرکت می‌کند (کوتاه‌ترین مسیر در گراف‌های بدون وزن).
  - *کاربرد:* سیستم پیشنهاد دوستان (Friends of Friends) یا محاسبه حداقل مسافت در نقشه‌ها.
- **Depth-First Search (DFS):** با استفاده از بازگشتی یا `Stack` تا انتهای یک شاخه می‌رود.
  - *کاربرد:* بررسی مجوزهای ارث‌بری شده در ساختار درختی سازمانی (RBAC)، یا یافتن تسک‌های وابسته (Dependency Resolution / Topological Sort).

---

## 📝 سوالات منتخب LeetCode برای تمرین جاوا بک‌اند
1. **Easy:** LeetCode #1 (Two Sum), #141 (Linked List Cycle), #20 (Valid Parentheses).
2. **Medium:** LeetCode #3 (Longest Substring Without Repeating Characters), #347 (Top K Frequent Elements), #200 (Number of Islands).
3. **Hard:** LeetCode #146 (LRU Cache) - *حیاتی‌ترین سوال مصاحبه‌های مهندسی بک‌اند!*
