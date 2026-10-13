<div align="center">

# 🛡️ PasarGuard · KataBump Edition

**PasarGuard panel + PasarGuard node + Xray-core, behind a single public port.**
**Two files. Zero dependencies. One command.**

![License](https://img.shields.io/badge/license-MIT-green?style=for-the-badge)
![Node](https://img.shields.io/badge/node-%3E%3D16-339933?style=for-the-badge&logo=node.js&logoColor=white)
![Dependencies](https://img.shields.io/badge/npm%20deps-0-brightgreen?style=for-the-badge)
![Files](https://img.shields.io/badge/files-2-blue?style=for-the-badge)
![Platform](https://img.shields.io/badge/platform-KataBump-purple?style=for-the-badge)

**🌐 Languages:**
[English](#-english) ·
[فارسی](#-فارسی) ·
[العربية](#-العربية) ·
[Русский](#-русский) ·
[中文](#-中文) ·
[Türkçe](#-türkçe)

</div>

---

## 🇬🇧 English

### ✨ What is this?

A modified build of [PasarGuard](https://github.com/PasarGuard/panel) that boots on **KataBump** from just two files: `index.js` and `package.json`. KataBump gives every new account a **4-day free credit**, so you can run a full PasarGuard panel in that time without buying a plan.

Everything is installed, configured and wired together automatically on first start.

### 🚀 Features

- 📦 **Two-file deployment**: `index.js` + `package.json`, no `npm install` needed
- 🔌 **Single public port**: panel, API, dashboard and the proxy tunnel all share one port
- 🧩 **Fully automatic setup**: downloads the panel source, Python, the node binary and Xray-core
- 🐍 **Portable Python**: fetches CPython 3.14 only if the server doesn't have a suitable one
- 🔗 **Node auto-linked** to the panel over a local self-signed TLS connection
- 📡 **Ready-made inbound** `KATABUMP_VLESS_WS` (VLESS over WebSocket) and an auto-created host entry
- 👤 **Admin auto-created**, credentials saved to `pasarguard/PANEL.txt`
- 🗃️ **SQLite database**, persistent across restarts
- 🧠 **Low-memory friendly**: Node heap limited to 48 MB and the panel is restarted if it exceeds a configurable cap
- 🩺 **Status endpoint** at `/_kb/status`
- 🤖 Optional **Telegram bot** support
- 🔧 Everything tunable through environment variables

### 📋 Requirements

- A [KataBump](https://katabump.com) account (sign up and use the free credit)
- Node.js **16+** (provided by KataBump)

### 🚀 Quick start

1. Create a **Node.js** server on KataBump.
2. Upload `index.js` and `package.json` to the server root.
3. Set the startup command to:
   ```bash
   npm start
   ```
4. Start the server. The **first boot takes a few minutes** while everything is downloaded and installed.
5. Open the panel:
   ```
   http://YOUR-SERVER-IP:PORT/dashboard/
   ```

### 🔑 Login

| Field | Value |
|---|---|
| Username | `admin` (change with `ADMIN_USER`) |
| Password | random, or the value of `ADMIN_PASS` |

Credentials are printed in the console and saved in `pasarguard/PANEL.txt`.

### 🔗 Endpoints

| Path | Purpose |
|---|---|
| `/dashboard/` | Web panel |
| `/docs` | API documentation |
| `/_kb/status` | Service status |

### ⚙️ Environment variables

All optional.

**Panel and access**

| Variable | Default | Description |
|---|---|---|
| `ADMIN_USER` | `admin` | Admin username |
| `ADMIN_PASS` | random | Admin password |
| `PASARGUARD_PUBLIC_HOST` | auto-detected | Public domain or IP used in links |
| `PASARGUARD_TELEGRAM_BOT` | `0` | `1` enables the Telegram bot |
| `PASARGUARD_AUTO_GROUP` | `1` | `0` disables automatic group creation |

**Tunnels and ports**

| Variable | Default | Description |
|---|---|---|
| `SERVER_PORT` / `PORT` | `3000` | Public port |
| `PASARGUARD_TUNNELS` | `vless:/mb-vless:21001` | `protocol:path:port`, comma-separated |
| `PANEL_PORT` | `8000` | Internal panel port |
| `PASARGUARD_NODE_PORT` | `62050` | Internal node port |
| `PASARGUARD_NODE_API_PORT` | `62051` | Internal node API port |
| `PASARGUARD_XRAY_PORT` | `21001` | Internal Xray port |

**Versions**

| Variable | Default | Description |
|---|---|---|
| `PASARGUARD_VERSION` | `v5.4.1` | Panel version |
| `PASARGUARD_NODE_VERSION` | `v0.5.4` | Node version |
| `XRAY_VERSION` | `latest` | Xray-core version |
| `PASARGUARD_PYTHON_VERSION` | `3.14.8` | Portable Python version |

**Behavior**

| Variable | Default | Description |
|---|---|---|
| `PASARGUARD_PANEL_MEM_MAX` | `205` | Restart the panel above this many MB |
| `PASARGUARD_FORCE_SETUP` | `0` | `1` forces a fresh setup |
| `PASARGUARD_SKIP_XRAY` | `0` | `1` skips the Xray install |
| `KATABUMP_ROOT` | current dir | Where the `pasarguard/` folder lives |

Example:

```bash
ADMIN_USER=boss ADMIN_PASS='S0me-Strong-Pass' npm start
```

### 🗂️ Directory layout

Everything lives under `pasarguard/`:

```
pasarguard/
├── app/          panel source (patched)
├── venv/         Python virtualenv
├── python/       portable CPython (only if needed)
├── node/         PasarGuard node binary + .env
├── xray-core/    Xray-core + geoip/geosite
├── certs/        self-signed cert for the local node
├── data/         SQLite database  ← back this up
├── log/          panel / node / xray logs
└── PANEL.txt     panel URL and credentials
```

> ⚠️ **Never delete `pasarguard/data`.** It holds your users and settings.

### 🏗️ How it works

```
                 ┌────────────────────────────── KataBump server ──────────────────────────────┐
 Internet ──────▶│  index.js  (public port)                                                    │
                 │     ├── /dashboard, /docs, /api ──▶ PasarGuard panel   (127.0.0.1:8000)     │
                 │     ├── /_kb/status ───────────────▶ built-in status                        │
                 │     └── /mb-vless (WebSocket) ─────▶ Xray-core         (127.0.0.1:21001)    │
                 │                                          ▲                                  │
                 │                    PasarGuard node ──────┘  (127.0.0.1:62050, local TLS)    │
                 └─────────────────────────────────────────────────────────────────────────────┘
```

### 🧪 Self-test

```bash
npm run selftest
```

### ❓ Troubleshooting

| Problem | Fix |
|---|---|
| Panel doesn't open | Wait a few minutes on first boot, then check `/_kb/status` |
| Forgot the password | Read `pasarguard/PANEL.txt` |
| Panel keeps restarting | The server has little RAM; tune `PASARGUARD_PANEL_MEM_MAX` |
| Broken install | Set `PASARGUARD_FORCE_SETUP=1` for one start, then remove it |
| Wrong links in the panel | Set `PASARGUARD_PUBLIC_HOST` to your domain or IP |

### 🤝 Contributing

Issues and pull requests are welcome. If this project helped you, a ⭐ is appreciated.

### ⚠️ Disclaimer

This project is provided for **educational and personal use**. You are responsible for complying with KataBump's terms of service and the laws of your country. It is provided "as is", without warranty.

---

## 🇮🇷 فارسی

<div dir="rtl">

### ✨ این پروژه چیست؟

نسخه‌ی تغییر‌یافته‌ی [PasarGuard](https://github.com/PasarGuard/panel) که فقط با **دو فایل** (`index.js` و `package.json`) روی **KataBump** بالا می‌آید. کاتابامپ به هر حساب جدید یک **کردیت رایگان ۴ روزه** می‌دهد و می‌توانید در همین مدت یک پنل کامل پاسارگارد را بدون خرید اشتراک اجرا کنید.

همه‌چیز در اولین اجرا خودکار نصب، تنظیم و به هم وصل می‌شود.

### 🚀 امکانات

- 📦 **نصب با دو فایل**، بدون نیاز به `npm install`
- 🔌 **یک پورت عمومی** برای پنل، API، داشبورد و تونل
- 🧩 **نصب کاملاً خودکار**: سورس پنل، پایتون، نود و Xray-core
- 🐍 **پایتون قابل‌حمل**: در صورت نبودن نسخه‌ی مناسب روی سرور، CPython 3.14 دانلود می‌شود
- 🔗 **اتصال خودکار نود به پنل** با TLS محلی
- 📡 **inbound آماده** با نام `KATABUMP_VLESS_WS` (VLESS روی WebSocket) و ساخت خودکار هاست
- 👤 **ساخت خودکار ادمین** و ذخیره‌ی اطلاعات در `pasarguard/PANEL.txt`
- 🗃️ **دیتابیس SQLite** ماندگار
- 🧠 **سبک برای رم کم**: محدودیت ۴۸ مگابایت برای Node و ری‌استارت پنل در صورت عبور از سقف رم
- 🩺 **صفحه‌ی وضعیت** در `/_kb/status`
- 🤖 پشتیبانی اختیاری از **ربات تلگرام**
- 🔧 تنظیم همه‌چیز با متغیرهای محیطی

### 📋 پیش‌نیازها

- حساب [KataBump](https://katabump.com) (ثبت‌نام و استفاده از کردیت رایگان)
- Node.js نسخه‌ی **۱۶ یا بالاتر** (خود کاتابامپ فراهم می‌کند)

### 🚀 نصب سریع

1. در KataBump یک سرور **Node.js** بسازید.
2. فایل‌های `index.js` و `package.json` را در پوشه‌ی اصلی سرور آپلود کنید.
3. دستور شروع را روی `npm start` بگذارید.
4. سرور را Start کنید. **اولین اجرا چند دقیقه طول می‌کشد.**
5. پنل را باز کنید:
   ```
   http://YOUR-SERVER-IP:PORT/dashboard/
   ```

### 🔑 ورود

- نام کاربری: `admin` (با `ADMIN_USER` عوض می‌شود)
- رمز: تصادفی یا مقدار `ADMIN_PASS`
- اطلاعات در کنسول و فایل `pasarguard/PANEL.txt` هست.

### 🔗 مسیرها

| مسیر | کاربرد |
|---|---|
| `/dashboard/` | پنل مدیریت |
| `/docs` | مستندات API |
| `/_kb/status` | وضعیت سرویس |

### ⚙️ متغیرهای محیطی (همگی اختیاری)

| متغیر | پیش‌فرض | توضیح |
|---|---|---|
| `ADMIN_USER` | `admin` | نام کاربری ادمین |
| `ADMIN_PASS` | تصادفی | رمز ادمین |
| `PASARGUARD_PUBLIC_HOST` | خودکار | دامنه یا IP عمومی برای لینک‌ها |
| `SERVER_PORT` / `PORT` | `3000` | پورت عمومی |
| `PASARGUARD_TUNNELS` | `vless:/mb-vless:21001` | `پروتکل:مسیر:پورت` (چندتا با کاما) |
| `PASARGUARD_VERSION` | `v5.4.1` | نسخه‌ی پنل |
| `PASARGUARD_NODE_VERSION` | `v0.5.4` | نسخه‌ی نود |
| `XRAY_VERSION` | `latest` | نسخه‌ی Xray-core |
| `PASARGUARD_PANEL_MEM_MAX` | `205` | سقف رم پنل (MB)؛ بیشتر از آن ری‌استارت می‌شود |
| `PASARGUARD_TELEGRAM_BOT` | `0` | با `1` ربات تلگرام فعال می‌شود |
| `PASARGUARD_FORCE_SETUP` | `0` | با `1` نصب از نو انجام می‌شود |
| `PASARGUARD_SKIP_XRAY` | `0` | با `1` نصب Xray رد می‌شود |
| `KATABUMP_ROOT` | پوشه‌ی جاری | محل پوشه‌ی `pasarguard/` |

### 🗂️ ساختار فایل‌ها

```
pasarguard/
├── app/          سورس پنل
├── venv/         محیط مجازی پایتون
├── node/         باینری نود
├── xray-core/    هسته‌ی Xray
├── data/         دیتابیس SQLite  ← بکاپ بگیرید
├── log/          لاگ‌ها
└── PANEL.txt     آدرس و اطلاعات ورود
```

> ⚠️ پوشه‌ی `pasarguard/data` را **هرگز حذف نکنید**؛ کاربران و تنظیمات شما آنجاست.

### ❓ عیب‌یابی

- **پنل باز نمی‌شود:** در اولین اجرا چند دقیقه صبر کنید و `/_kb/status` را ببینید.
- **رمز را فراموش کردم:** فایل `pasarguard/PANEL.txt` را بخوانید.
- **پنل مدام ری‌استارت می‌شود:** رم کم است؛ `PASARGUARD_PANEL_MEM_MAX` را تنظیم کنید.
- **نصب خراب شد:** یک بار `PASARGUARD_FORCE_SETUP=1` بگذارید و بعد حذفش کنید.

### ⚠️ سلب مسئولیت

این پروژه برای **اهداف آموزشی و استفاده‌ی شخصی** است. رعایت شرایط استفاده‌ی KataBump و قوانین کشورتان بر عهده‌ی شماست. پروژه «همان‌گونه که هست» و بدون هیچ ضمانتی ارائه می‌شود.

</div>

---

## 🇸🇦 العربية

<div dir="rtl">

### ✨ ما هذا المشروع؟

نسخة معدّلة من [PasarGuard](https://github.com/PasarGuard/panel) تعمل على **KataBump** بملفين فقط: `index.js` و `package.json`. تمنح KataBump كل حساب جديد **رصيدًا مجانيًا لمدة 4 أيام**، يمكنك خلالها تشغيل لوحة PasarGuard كاملة دون شراء اشتراك.

يتم تثبيت كل شيء وإعداده وربطه تلقائيًا عند أول تشغيل.

### 🚀 المميزات

- 📦 نشر بملفين فقط، دون الحاجة إلى `npm install`
- 🔌 منفذ عام واحد للوحة وواجهة API ولوحة المعلومات والنفق
- 🧩 إعداد تلقائي كامل: اللوحة وPython والعقدة (node) وXray-core
- 🔗 ربط العقدة باللوحة تلقائيًا عبر TLS محلي
- 📡 inbound جاهز باسم `KATABUMP_VLESS_WS` (VLESS عبر WebSocket)
- 👤 إنشاء حساب المدير تلقائيًا وحفظ البيانات في `pasarguard/PANEL.txt`
- 🗃️ قاعدة بيانات SQLite دائمة
- 🧠 مناسب للذاكرة المنخفضة مع إعادة تشغيل اللوحة عند تجاوز الحد
- 🩺 صفحة الحالة `/_kb/status`

### 🚀 البدء السريع

1. أنشئ خادم **Node.js** على KataBump.
2. ارفع `index.js` و`package.json` إلى المجلد الرئيسي.
3. اجعل أمر التشغيل `npm start`.
4. شغّل الخادم. **التشغيل الأول يستغرق بضع دقائق.**
5. افتح اللوحة:
   ```
   http://YOUR-SERVER-IP:PORT/dashboard/
   ```

اسم المستخدم الافتراضي `admin`، وكلمة المرور عشوائية ما لم تضبط `ADMIN_PASS`. تجدها في سجل الكونسول وفي الملف `pasarguard/PANEL.txt`.

### ⚙️ أهم المتغيرات

| المتغير | الافتراضي | الوصف |
|---|---|---|
| `ADMIN_USER` | `admin` | اسم مستخدم المدير |
| `ADMIN_PASS` | عشوائي | كلمة مرور المدير |
| `PASARGUARD_PUBLIC_HOST` | تلقائي | النطاق أو IP العام |
| `PASARGUARD_TUNNELS` | `vless:/mb-vless:21001` | `البروتوكول:المسار:المنفذ` |
| `PASARGUARD_PANEL_MEM_MAX` | `205` | حد ذاكرة اللوحة (MB) |
| `PASARGUARD_TELEGRAM_BOT` | `0` | `1` لتفعيل بوت تيليجرام |

> ⚠️ لا تحذف المجلد `pasarguard/data` أبدًا، ففيه المستخدمون والإعدادات.

### ⚠️ إخلاء المسؤولية

المشروع للأغراض التعليمية والاستخدام الشخصي. أنت مسؤول عن الالتزام بشروط خدمة KataBump وقوانين بلدك. يُقدَّم «كما هو» دون أي ضمان.

</div>

---

## 🇷🇺 Русский

### ✨ Что это?

Модифицированная сборка [PasarGuard](https://github.com/PasarGuard/panel), которая запускается на **KataBump** всего из двух файлов: `index.js` и `package.json`. KataBump дарит новым аккаунтам **бесплатный кредит на 4 дня**, за это время можно поднять полноценную панель PasarGuard без покупки тарифа.

Всё устанавливается, настраивается и связывается автоматически при первом запуске.

### 🚀 Возможности

- 📦 Два файла, без `npm install`
- 🔌 Один публичный порт для панели, API, дашборда и туннеля
- 🧩 Полностью автоматическая установка: панель, Python, нода, Xray-core
- 🔗 Нода автоматически подключается к панели по локальному TLS
- 📡 Готовый inbound `KATABUMP_VLESS_WS` (VLESS поверх WebSocket)
- 👤 Администратор создаётся автоматически, данные сохраняются в `pasarguard/PANEL.txt`
- 🗃️ Постоянная база SQLite
- 🧠 Рассчитано на малый объём RAM: панель перезапускается при превышении лимита
- 🩺 Страница статуса `/_kb/status`

### 🚀 Быстрый старт

1. Создайте **Node.js**-сервер на KataBump.
2. Загрузите `index.js` и `package.json` в корень сервера.
3. Команда запуска: `npm start`.
4. Запустите сервер. **Первый запуск занимает несколько минут.**
5. Откройте панель:
   ```
   http://YOUR-SERVER-IP:PORT/dashboard/
   ```

Логин по умолчанию `admin`. Если `ADMIN_PASS` не задан, пароль генерируется случайно и записывается в консоль и в `pasarguard/PANEL.txt`.

### ⚙️ Основные переменные

| Переменная | По умолчанию | Описание |
|---|---|---|
| `ADMIN_USER` | `admin` | Логин администратора |
| `ADMIN_PASS` | случайный | Пароль администратора |
| `PASARGUARD_PUBLIC_HOST` | авто | Публичный домен или IP |
| `PASARGUARD_TUNNELS` | `vless:/mb-vless:21001` | `протокол:путь:порт` |
| `PASARGUARD_PANEL_MEM_MAX` | `205` | Лимит памяти панели (МБ) |
| `PASARGUARD_TELEGRAM_BOT` | `0` | `1` включает Telegram-бота |

> ⚠️ Никогда не удаляйте `pasarguard/data`: там ваши пользователи и настройки.

### ⚠️ Отказ от ответственности

Проект предназначен для образовательных целей и личного использования. Вы сами отвечаете за соблюдение условий KataBump и законов вашей страны. Предоставляется «как есть», без гарантий.

---

## 🇨🇳 中文

### ✨ 这是什么?

这是 [PasarGuard](https://github.com/PasarGuard/panel) 的修改版,只需两个文件(`index.js` 和 `package.json`)即可在 **KataBump** 上运行。KataBump 会给新账号 **4 天的免费额度**,你可以在这段时间内免费运行完整的 PasarGuard 面板,无需购买套餐。

首次启动时,所有组件会自动安装、配置并相互连接。

### 🚀 特性

- 📦 仅需两个文件,无需 `npm install`
- 🔌 面板、API、仪表盘和隧道共用一个公网端口
- 🧩 全自动安装:面板、Python、节点、Xray-core
- 🔗 节点通过本地 TLS 自动连接面板
- 📡 预置 inbound `KATABUMP_VLESS_WS`(VLESS over WebSocket)
- 👤 自动创建管理员,凭据保存在 `pasarguard/PANEL.txt`
- 🗃️ 持久化 SQLite 数据库
- 🧠 适合低内存环境,超过上限时自动重启面板
- 🩺 状态页 `/_kb/status`

### 🚀 快速开始

1. 在 KataBump 创建一个 **Node.js** 服务器。
2. 将 `index.js` 和 `package.json` 上传到服务器根目录。
3. 启动命令设为 `npm start`。
4. 启动服务器。**首次启动需要几分钟。**
5. 打开面板:
   ```
   http://YOUR-SERVER-IP:PORT/dashboard/
   ```

默认用户名为 `admin`。未设置 `ADMIN_PASS` 时会生成随机密码,并输出到控制台和 `pasarguard/PANEL.txt`。

### ⚙️ 主要环境变量

| 变量 | 默认值 | 说明 |
|---|---|---|
| `ADMIN_USER` | `admin` | 管理员用户名 |
| `ADMIN_PASS` | 随机 | 管理员密码 |
| `PASARGUARD_PUBLIC_HOST` | 自动 | 公网域名或 IP |
| `PASARGUARD_TUNNELS` | `vless:/mb-vless:21001` | `协议:路径:端口` |
| `PASARGUARD_PANEL_MEM_MAX` | `205` | 面板内存上限(MB) |
| `PASARGUARD_TELEGRAM_BOT` | `0` | 设为 `1` 启用 Telegram 机器人 |

> ⚠️ 请勿删除 `pasarguard/data`,用户和设置都保存在这里。

### ⚠️ 免责声明

本项目仅供学习和个人使用。请自行遵守 KataBump 的服务条款及所在国家的法律。按「现状」提供,不附带任何保证。

---

## 🇹🇷 Türkçe

### ✨ Bu nedir?

[PasarGuard](https://github.com/PasarGuard/panel) için değiştirilmiş bir sürüm; **KataBump** üzerinde yalnızca iki dosyayla (`index.js` ve `package.json`) çalışır. KataBump yeni hesaplara **4 günlük ücretsiz kredi** verir; bu sürede paket satın almadan eksiksiz bir PasarGuard paneli çalıştırabilirsiniz.

İlk başlatmada her şey otomatik olarak kurulur, yapılandırılır ve birbirine bağlanır.

### 🚀 Özellikler

- 📦 İki dosya, `npm install` gerekmez
- 🔌 Panel, API, gösterge paneli ve tünel için tek genel port
- 🧩 Tam otomatik kurulum: panel, Python, node, Xray-core
- 🔗 Node, yerel TLS ile panele otomatik bağlanır
- 📡 Hazır inbound: `KATABUMP_VLESS_WS` (WebSocket üzerinden VLESS)
- 👤 Yönetici otomatik oluşturulur, bilgiler `pasarguard/PANEL.txt` dosyasına yazılır
- 🗃️ Kalıcı SQLite veritabanı
- 🧠 Düşük RAM için uygun, sınır aşılırsa panel yeniden başlatılır
- 🩺 Durum sayfası: `/_kb/status`

### 🚀 Hızlı başlangıç

1. KataBump'ta bir **Node.js** sunucusu oluşturun.
2. `index.js` ve `package.json` dosyalarını sunucunun kök dizinine yükleyin.
3. Başlatma komutunu `npm start` yapın.
4. Sunucuyu başlatın. **İlk açılış birkaç dakika sürer.**
5. Paneli açın:
   ```
   http://YOUR-SERVER-IP:PORT/dashboard/
   ```

Varsayılan kullanıcı adı `admin`'dir. `ADMIN_PASS` ayarlanmazsa rastgele bir parola üretilir ve konsola ile `pasarguard/PANEL.txt` dosyasına yazılır.

### ⚙️ Başlıca ortam değişkenleri

| Değişken | Varsayılan | Açıklama |
|---|---|---|
| `ADMIN_USER` | `admin` | Yönetici kullanıcı adı |
| `ADMIN_PASS` | rastgele | Yönetici parolası |
| `PASARGUARD_PUBLIC_HOST` | otomatik | Genel alan adı veya IP |
| `PASARGUARD_TUNNELS` | `vless:/mb-vless:21001` | `protokol:yol:port` |
| `PASARGUARD_PANEL_MEM_MAX` | `205` | Panel bellek sınırı (MB) |
| `PASARGUARD_TELEGRAM_BOT` | `0` | `1` Telegram botunu açar |

> ⚠️ `pasarguard/data` klasörünü asla silmeyin; kullanıcılarınız ve ayarlarınız burada.

### ⚠️ Sorumluluk reddi

Bu proje eğitim amaçlı ve kişisel kullanım içindir. KataBump kullanım koşullarına ve ülkenizin yasalarına uymak sizin sorumluluğunuzdadır. "Olduğu gibi" sunulur, garanti verilmez.

---

<div align="center">

📄 **License:** MIT · Made with ❤️ for the community

⭐ If this helped you, star the repo!

</div>
