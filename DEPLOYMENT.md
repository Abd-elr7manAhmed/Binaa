# نشر موقع بناء بشكل مستقل

المشروع أصبح تطبيق ويب مستقلًا يعمل على Vercel، وتُحفظ بيانات الحسابات والمحتوى في PostgreSQL الخاص بمشروع Supabase مستقل. لا تستخدم PGlite في الإنتاج؛ يتوقف البناء/التشغيل إذا لم يتم ضبط `DATABASE_URL`.

## 1) قاعدة البيانات – Supabase

1. أنشئ مشروع Supabase مستقلًا باسم Binaa.
2. من **Project Settings → Database → Connection string** اختر URI المناسب للاتصال من الخوادم. استخدم Session Pooler إذا لم يكن اتصال Direct متاحًا من بيئة الاستضافة.
3. انسخ URI وضعه في متغير `DATABASE_URL` في Vercel. لا تضعه في كود الواجهة ولا تشارك Service Role Key.
4. أثناء أول Build، ينفذ `npm run build` ملفات migrations الموجودة في `migrations/` على قاعدة البيانات.

## 2) Google OAuth (اختياري – البريد الإلكتروني يعمل بدونه)

1. أنشئ OAuth 2.0 Web Client في Google Cloud Console.
2. أضف Authorized redirect URI: `https://YOUR_DOMAIN/api/auth/callback/google`.
3. أضف `GOOGLE_CLIENT_ID` و`GOOGLE_CLIENT_SECRET` كمتغيرات بيئة في Vercel.
4. لا يوجد تسجيل دخول عبر X في هذه النسخة.

## 3) النشر على Vercel من المتصفح

1. ارفع مجلد المشروع إلى GitHub repository خاص.
2. افتح Vercel واختر **Add New → Project** ثم اربط مستودع GitHub.
3. أضف متغيرات البيئة التالية في إعدادات المشروع (Production وPreview حسب حاجتك):
   - `DATABASE_URL` = رابط PostgreSQL الخاص بـ Supabase.
   - `BETTER_AUTH_URL` = رابط الموقع النهائي مثل `https://binaa.example.com`.
   - `BETTER_AUTH_SECRET` = قيمة عشوائية قوية وطويلة (32 بايت على الأقل).
   - `VITE_AUTH_ENABLED` = `true`.
   - `GOOGLE_CLIENT_ID` و`GOOGLE_CLIENT_SECRET` لتفعيل Google.
4. استخدم إعدادات البناء الموجودة في `vercel.json` ثم اضغط Deploy.
5. بعد النشر، تأكد أن `BETTER_AUTH_URL` يطابق الرابط الفعلي حرفيًا، وأن رابط Google callback في Google Cloud يطابق `https://YOUR_DOMAIN/api/auth/callback/google`.
6. افتح الموقع على الكمبيوتر والموبايل وسجّل الدخول بنفس الحساب؛ ستُقرأ البيانات من قاعدة البيانات المشتركة.
7. إذا كانت لديك بيانات مهمة في نسخة الاختبار المحلية، صدّرها من **الإعدادات → تصدير نسخة** قبل الانتقال، ثم استورد ملف JSON بعد إنشاء حسابك على الموقع المنشور.

## 4) تشغيل محلي للاختبار فقط

يمكن استخدام `npm install` ثم `npm run dev` للتطوير المحلي. هذا ليس أسلوب الاستخدام النهائي للموقع المنشور.

## متغيرات البيئة

راجع `.env.example`. لا ترفع ملف `.env` أو أي أسرار إلى GitHub.
