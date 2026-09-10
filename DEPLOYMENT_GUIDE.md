# راهنمای استقرار خودکار Worker روی Cloudflare

این مخزن طوری پیکربندی شده که **بدون نیاز به هیچ Secret یا API Token** به صورت کاملاً خودکار روی Cloudflare Workers مستقر شود.

## 🔐 احراز هویت با GitHub OIDC

این پروژه از **GitHub OIDC** برای احراز هویت بدون پسورد استفاده می‌کند. این یعنی:
- ❌ نیازی به ذخیره API Token در GitHub Secrets نیست
- ✅ امنیت بالاتر (Tokenها منقضی نمی‌شوند)
- ✅ استقرار کاملاً خودکار با هر push به شاخه `main`

## 📋 مراحل تنظیم در Cloudflare

### ۱. ساخت API Token

1. به آدرس https://dash.cloudflare.com/profile/api-tokens بروید
2. روی **"Create API Token"** کلیک کنید
3. قالب **"Edit Cloudflare Workers"** را انتخاب کنید
4. روی **"Continue to summary"** کلیک کنید

### ۲. اضافه کردن GitHub OIDC Trust

در صفحه Summary، بخش **Advanced** را باز کنید و گزینه **"Add GitHub OIDC Trust"** را بزنید:

| فیلد | مقدار |
|------|-------|
| **Organization** | نام کاربری گیت‌هاب شما |
| **Repository** | نام مخزن |
| **Branch** | `main` |

### ۳. ذخیره Token

- روی **"Create Token"** کلیک کنید
- Token نمایش داده می‌شود اما **نیازی به کپی کردن آن نیست**
- فقط مطمئن شوید Token ساخته شده است
- Cloudflare حالا به GitHub شما اعتماد می‌کند

## 🚀 استقرار خودکار

پس از انجام تنظیمات بالا، هر بار که به شاخه `main` پوش کنید:

```bash
git add .
git commit -m "your changes"
git push origin main
```

GitHub Actions به صورت خودکار:
1. ✅ با Cloudflare احراز هویت می‌کند (از طریق OIDC)
2. ✅ KV Namespace می‌سازد (اگر وجود نداشته باشد)
3. ✅ Worker را مستقر می‌کند
4. ✅ URL Worker را در Actions نمایش می‌دهد

## 📝 نکات مهم

- اولین بار که deploy می‌کنید، یک KV Namespace به صورت خودکار ساخته می‌شود
- ID این KV در فایل `wrangler.toml` ذخیره و commit می‌شود
- Worker شما در آدرس `https://<worker-name>.workers.dev` قابل دسترسی است

## 🔍 عیب‌یابی

اگر deployment failed شد:

1. مطمئن شوید OIDC Trust را درست تنظیم کرده‌اید
2. بررسی کنید Organization و Repository name درست وارد شده باشد
3. Branch باید دقیقاً `main` باشد
4. لاگ‌های GitHub Actions را بررسی کنید

---

**✅ تمام! حالا مخزن شما کاملاً خودکار و بدون نیاز به Secretها مستقر می‌شود.**
