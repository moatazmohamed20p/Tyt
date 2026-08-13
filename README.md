# رادار الأرقام

موقع تحقق من أرقام الهواتف باستخدام Numverify API.

## الملفات
- `index.html` — الواجهة (تصميم داكن ذهبي، RTL)
- `proxy.php` — بروكسي بيخبي الـ Access Key ويحل مشكلة Mixed Content

## التشغيل

1. تأكد إن السيرفر شغّال PHP مع تفعيل cURL.
2. حدد متغير البيئة `NUMVERIFY_KEY` بمفتاحك (منه أبدًا متحطوش جوه الكود):

   ```bash
   # تجربة محلية
   NUMVERIFY_KEY=your_key_here php -S localhost:8000
   ```

   على Apache (.htaccess أو vhost):
   ```
   SetEnv NUMVERIFY_KEY your_key_here
   ```

   على استضافات زي Render / Railway / Hostinger: من لوحة **Environment Variables**.

3. افتح `index.html` — هتلاقيه بيكلم `proxy.php` تلقائيًا.

## ملحوظة أمان
متنشرش المفتاح أبدًا في `index.html` أو في أي كود فرونت-إند — ده اللي البروكسي أصلاً موجود عشانه.
