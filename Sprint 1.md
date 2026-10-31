### ۳.۴ جدول courses (دوره‌ها)

| ستون | نوع | توضیح |
|---|---|---|
| id | BIGINT PRIMARY KEY | شناسهٔ دوره |
| teacher_id | BIGINT NOT NULL | شناسهٔ استاد |
| category_id | BIGINT NOT NULL | شناسهٔ دسته‌بندی |
| title | VARCHAR(200) NOT NULL | عنوان دوره |
| description | TEXT | توضیحات دوره |
| level | VARCHAR(20) NOT NULL | beginner / intermediate / advanced |
| price | NUMERIC(10,2) NOT NULL | قیمت دوره |
| status | VARCHAR(20) NOT NULL | draft / published / archived |
| cover_image_url | VARCHAR(500) | نشانی تصویر دوره |
| created_at | TIMESTAMPTZ | تاریخ ایجاد |
| updated_at | TIMESTAMPTZ | تاریخ آخرین ویرایش |

### ۳.۵ جدول lessons (درس‌ها)

| ستون | نوع | توضیح |
|---|---|---|
| id | BIGINT PRIMARY KEY | شناسهٔ درس |
| course_id | BIGINT NOT NULL | شناسهٔ دوره |
| title | VARCHAR(200) NOT NULL | عنوان درس |
| description | TEXT | توضیحات درس |
| content_type | VARCHAR(20) NOT NULL | video / audio / text |
| content_url | VARCHAR(1000) | نشانی محتوای آموزشی |
| duration_seconds | INTEGER | مدت محتوا به ثانیه |
| lesson_order | INTEGER NOT NULL | ترتیب نمایش درس |
| is_preview | BOOLEAN DEFAULT FALSE | پیش‌نمایش رایگان |
| created_at | TIMESTAMPTZ | تاریخ ایجاد |

### ۳.۶ جدول enrollments (ثبت‌نام‌ها)

| ستون | نوع | توضیح |
|---|---|---|
| id | BIGINT PRIMARY KEY | شناسهٔ ثبت‌نام |
| student_id | BIGINT NOT NULL | شناسهٔ هنرجو |
| course_id | BIGINT NOT NULL | شناسهٔ دوره |
| enrolled_at | TIMESTAMPTZ | زمان ثبت‌نام |
| status | VARCHAR(20) NOT NULL | active / completed / cancelled |
| paid_amount | NUMERIC(10,2) | مبلغ پرداخت‌شده، در صورت نیاز |

ترکیب student_id و course_id باید یکتا باشد تا از ثبت‌نام تکراری جلوگیری شود.

### ۳.۷ جدول favorites (علاقه‌مندی‌ها)

| ستون | نوع | توضیح |
|---|---|---|
| id | BIGINT PRIMARY KEY | شناسهٔ رکورد |
| user_id | BIGINT NOT NULL | شناسهٔ کاربر |
| course_id | BIGINT NOT NULL | شناسهٔ دوره |
| created_at | TIMESTAMPTZ | زمان افزودن |

ترکیب user_id و course_id باید یکتا باشد تا یک دوره برای یک کاربر چند بار ذخیره نشود.

### ۳.۸ جدول lesson_progress (پیشرفت درس‌ها)

| ستون | نوع | توضیح |
|---|---|---|
| id | BIGINT PRIMARY KEY | شناسهٔ رکورد |
| student_id | BIGINT NOT NULL | شناسهٔ هنرجو |
| lesson_id | BIGINT NOT NULL | شناسهٔ درس |
| status | VARCHAR(20) NOT NULL | not_started / in_progress / completed |
| last_position_seconds | INTEGER | آخرین موقعیت پخش |
| completed_at | TIMESTAMPTZ | زمان تکمیل درس |
| updated_at | TIMESTAMPTZ | زمان آخرین به‌روزرسانی |

ترکیب student_id و lesson_id باید یکتا باشد.

---

## ۴. روابط

### ۴.۱ یک‌به‌چند (1:N)

| رابطه | توضیح |
|---|---|
| teachers → courses | هر استاد می‌تواند چند دوره ارائه کند. |
| categories → courses | هر دسته می‌تواند شامل چند دوره باشد. |
| courses → lessons | هر دوره می‌تواند چند درس داشته باشد. |
| users → enrollments | هر هنرجو می‌تواند در چند دوره ثبت‌نام کند. |
| courses → enrollments | هر دوره می‌تواند چند هنرجو داشته باشد. |
| users → lesson_progress | هر هنرجو می‌تواند چند رکورد پیشرفت داشته باشد. |
| lessons → lesson_progress | هر درس می‌تواند برای چند هنرجو رکورد پیشرفت داشته باشد. |

### ۴.۲ چندبه‌چند (N:M)

| رابطه | جدول واسط | توضیح |
|---|---|---|
| users ↔ courses | enrollments | هنرجویان می‌توانند در چند دوره ثبت‌نام کنند. |
| users ↔ courses | favorites | کاربران می‌توانند چند دوره را به علاقه‌مندی‌ها اضافه کنند. |

---

## ۵. ایندکس‌ها

### ۵.۱ Primary Keys

- users.id
- teachers.id
- categories.id
- courses.id
- lessons.id
- enrollments.id
- favorites.id
- lesson_progress.id

### ۵.۲ Foreign Keys

- teachers.user_id → users.id
- courses.teacher_id → teachers.id
- courses.category_id → categories.id
- lessons.course_id → courses.id
- enrollments.student_id → users.id
- enrollments.course_id → courses.id
- favorites.user_id → users.id
- favorites.course_id → courses.id
- lesson_progress.student_id → users.id
- lesson_progress.lesson_id → lessons.id

### ۵.۳ ایندکس‌های اضافی
| جدول | ستون | دلیل |
|---|---|---|
| users | email | جست‌وجوی حساب و جلوگیری از ایمیل تکراری |
| courses | category_id | فیلتر دوره‌ها بر اساس ساز یا دسته |
| courses | teacher_id | نمایش دوره‌های یک استاد |
| courses | status | فیلتر دوره‌های منتشرشده |
| lessons | course_id | دریافت درس‌های یک دوره |
| enrollments | student_id | نمایش ثبت‌نام‌های هنرجو |
| enrollments | course_id | نمایش هنرجویان یک دوره |
| favorites | user_id | نمایش علاقه‌مندی‌های کاربر |
| lesson_progress | student_id | بررسی پیشرفت هنرجو |

---

## ۶. نرمال‌سازی

### ۶.۱ 1NF — حذف گروه‌های تکراری

- هر ستون یک مقدار اتمی دارد.
- اطلاعات چند درس در یک ستون ذخیره نمی‌شود.
- هر درس در جدول lessons رکورد مستقل دارد.

### ۶.۲ 2NF — حذف وابستگی جزئی

- اطلاعات هر جدول به کلید اصلی مربوط وابسته است.
- اطلاعات ثبت‌نام، علاقه‌مندی و پیشرفت در جدول‌های جداگانه نگهداری می‌شوند.

### ۶.۳ 3NF — حذف وابستگی انتقالی

- اطلاعات استادان در جدول teachers نگهداری می‌شود.
- اطلاعات دسته‌بندی‌ها در جدول categories نگهداری می‌شود.
- اطلاعات دوره‌ها و درس‌ها در جدول‌های مستقل قرار دارند.

نتیجه: طراحی پیشنهادی با هدف رعایت اصول نرمال‌سازی تا 3NF انجام شده است.

---

## ۷. امنیت داده

### ۷.۱ رمزنگاری

- گذرواژه‌ها با bcrypt یا Argon2id هش می‌شوند.
- ارتباط با پایگاه داده در محیط عملیاتی با SSL/TLS محافظت می‌شود.
- رمزهای اتصال دیتابیس نباید در مخزن Git قرار بگیرند.

### ۷.۲ Backup

- پشتیبان‌گیری روزانه، متناسب با امکانات سرویس میزبانی.
- نگهداری نسخه‌های پشتیبان برای مدت پیشنهادی ۳۰ روز.
- بازیابی نسخهٔ پشتیبان باید به‌صورت دوره‌ای آزمایش شود.

### ۷.۳ دسترسی

- استفاده از حساب دیتابیس با حداقل دسترسی لازم.
- هنرجو فقط به اطلاعات ثبت‌نام و پیشرفت خودش دسترسی دارد.
- استاد فقط مجاز به مدیریت دوره‌ها و درس‌های خودش است.
- عملیات مدیریتی فقط برای مدیر سامانه مجاز است.
- کنترل نقش‌ها و مجوزها در سمت سرور انجام می‌شود.

---

## ۸. نمونهٔ Queryها

### ۸.۱ لیست دوره‌های منتشرشدهٔ یک ساز

SELECT
    c.id,
    c.title,
    c.level,
    c.price,
    u.full_name AS teacher_name
FROM courses c
JOIN categories cat ON cat.id = c.category_id
JOIN teachers t ON t.id = c.teacher_id
JOIN users u ON u.id = t.user_id
WHERE cat.slug = 'tar'
  AND c.status = 'published'
ORDER BY c.created_at DESC;
### ۸.۲ دوره‌های ثبت‌نام‌شدهٔ یک هنرجو

SELECT
    c.id,
    c.title,
    e.enrolled_at,
    e.status
FROM enrollments e
JOIN courses c ON c.id = e.course_id
WHERE e.student_id = 10
ORDER BY e.enrolled_at DESC;
### ۸.۳ پیشرفت یک هنرجو در درس‌ها

SELECT
    l.title AS lesson_title,
    lp.status,
    lp.last_position_seconds,
    lp.completed_at
FROM lesson_progress lp
JOIN lessons l ON l.id = lp.lesson_id
WHERE lp.student_id = 10
ORDER BY lp.updated_at DESC;
### ۸.۴ گزارش ثبت‌نام روزانه

SELECT
    DATE(enrolled_at) AS date,
    COUNT(*) AS enrollments,
    SUM(paid_amount) AS total_amount
FROM enrollments
WHERE status IN ('active', 'completed')
GROUP BY DATE(enrolled_at)
ORDER BY date DESC;
---

## تاریخچهٔ تغییرات

| نسخه | تاریخ | تغییرات | تهیه‌کننده |
|---|---|---|---|
| ۱.۰ | ۱۴۰۴ | نسخهٔ اولیهٔ طرح دیتابیس | [نام مسئول بخش دیتابیس] |



