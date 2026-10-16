<div dir="rtl">

# كشك صيدلية مهند

**Mohannad Pharmacy — In-store Touch Kiosk**

تطبيق شاشة لمس عمودية بقياس 43 بوصة داخل صيدلية في دمشق، متخصصة في منتجات العناية والتغذية. الشاشة تعمل معظم وقتها كلافتة رقمية (وضع الجذب)، وعند لمسها تتحوّل إلى كتالوج تفاعلي يتيح للزبون تصفّح المنتجات والتأكد من توفّرها وطلبها بمسح رمز QR يفتح واتساب الصيدلية.

> المحتوى المعروض (المنتجات والأسعار) **تجريبي** لأغراض العرض فقط، ولا يتضمّن أي ادعاء علاجي.

## محتويات المستودع

| المجلد | الوصف | الحالة |
| --- | --- | --- |
| [`kiosk-app/`](kiosk-app/index.html) | تطبيق الكشك الكامل: وضع الجذب + الكتالوج التفاعلي + شاشة الخدمة | **المنتج الحالي** |
| [`attract-mode/`](attract-mode/index.html) | نموذج أولي مستقل لوضع الجذب (حلقة شرائح إعلانية) | نموذج أولي |
| [`docs/architecture.html`](docs/architecture.html) | وثيقة المعمارية والتصميم بالعربية (31 بنداً + المصادر) | الإصدار 2 · أغسطس 2026 |
| [`prototype-pwa/`](prototype-pwa/index.html) | نسخة مبنية (build) من النموذج الأولي الأقدم كتطبيق ويب تقدّمي PWA | مؤرشف — أُلغي في أغسطس 2026 |

## المزايا الرئيسية — `kiosk-app`

- **نظام تصميم بالمليمتر:** كل الأحجام تُكتب بوحدة `--mm` المحسوبة من العرض الفعلي للوحة (535.7 × 952.4 مم)، فيبقى حجم الخط والأزرار ثابتاً فيزيائياً مهما كانت دقة الشاشة.
- **وضع الجذب:** دورة شرائح تلقائية بلا أي عنصر تحكّم مرئي، تعود إليها الشاشة بعد انتهاء كل جلسة.
- **كتالوج تفاعلي:** ستة أقسام و36 منتجاً تجريبياً، بحث بلوحة مفاتيح عربية/إنجليزية على الشاشة مع المصطلحات الأكثر بحثاً، وصفحة منتج تعرض الحجم والشكل والتوفّر.
- **الطلب عبر واتساب:** رمز QR يُولَّد وقت التشغيل لرابط `wa.me` برسالة جاهزة تحمل اسم المنتج. مُرمِّز QR مكتوب من الصفر بلا مكتبات (وضع البايت، مستوى تصحيح M، الإصدارات 1–20).
- **ثنائي اللغة:** عربي (RTL) وإنجليزي (LTR) بخصائص CSS المنطقية، مع عزل الأسماء اللاتينية داخل النص العربي.
- **إدارة الجلسة:** تنبيه بعد 45 ثانية من الخمول ثم عدّ تنازلي 15 ثانية قبل العودة لوضع الجذب.
- **شاشة خدمة مخفية:** تُفتح بلمس الزوايا الأربع خلال 3 ثوانٍ.
- **مزامنة المحتوى:** من جدول Google Sheets منشور بصيغة CSV كل 5 دقائق، مع نسخة مخبّأة في `localStorage`، ويعمل دون اتصال بالكتالوج المدمّج.
- **رايات الميزات التنظيمية:** الأقسام الدوائية تُعرض كوجود فقط، بلا أسعار ولا معلومات دواء ولا طلب، احتياطاً حتى تتضح موافقة وزارة الصحة وفق [القرار التنظيمي 24/ت تاريخ 2/12/2025](https://www.moh.gov.sy/legislation/decisions) الخاص بتطبيقات الصحة الإلكترونية.

## التشغيل

التطبيق ملف HTML واحد بلا أي اعتماديات أو خطوة بناء. افتح `kiosk-app/index.html` في المتصفح مباشرة، أو شغّل خادماً محلياً:

```bash
python3 -m http.server 8080
# ثم افتح http://localhost:8080/kiosk-app/
```

للمعاينة بأبعاد الكشك، اضبط نافذة المتصفح على نسبة عمودية 9:16 (مثلاً 1080 × 1920) من أدوات المطوّر.

## الإعداد

كل الإعدادات في أعلى `kiosk-app/index.html`:

| الثابت | الوظيفة |
| --- | --- |
| `BRAND.order` | رقم واتساب الطلبات بصيغة E.164. **الرقم الحالي تجريبي** ويجب استبداله برقم الصيدلية قبل التركيب |
| `SHEET_CSV` | رابط CSV لجدول Google Sheets المنشور. فارغاً يعمل الكشك بالكتالوج المدمّج |
| `SYNC_EVERY_MS` | فترة المزامنة (افتراضياً 5 دقائق) |
| `CAPABILITIES` | الأقسام القابلة للتصفّح، الأقسام للاستفسار فقط، ومهل الخمول |

أعمدة جدول المنتجات: `id, sector, name, brand, size, form, price, was, availability, featured` — والأعمدة الإلزامية هي `sector` و`name` و`price`. قيم `availability` المقبولة: `in` · `low` · `ask`، وقيم `sector`: `maternity` · `sports` · `beauty` · `equipment` · `oncology` · `general`.

## التقنيات

- HTML5 · CSS (Custom Properties، Logical Properties) · JavaScript (ES2020+) بلا أطر عمل.
- الخطوط: [Tajawal](https://fonts.google.com/specimen/Tajawal) للعناوين، و[IBM Plex Sans Arabic](https://fonts.google.com/specimen/IBM+Plex+Sans+Arabic) للنص، و IBM Plex Mono للأرقام.
- الهوية: الأزرق `#0d517e` والأخضر `#8ec341`.

## خطة النشر على الجهاز

- **العتاد:** كشك أرضي بشاشة لمس 43 بوصة عمودية (CSHW-ELED-LS43) يعمل بنظام Android.
- **الغلاف:** تطبيق Kotlin رفيع يعرض الواجهة داخل `WebView`، مع تفعيل [وضع Lock Task](https://developer.android.com/work/dpc/dedicated-devices/lock-task-mode) عبر Device Owner حتى لا يخرج المستخدم من التطبيق.
- التفاصيل الكاملة (العتاد، البيئة الفيزيائية، الأمان، العمل دون اتصال، التحليلات، إمكانية الوصول، خارطة الطريق) في [وثيقة المعمارية](docs/architecture.html).

## عن النموذج الأولي المؤرشف

`prototype-pwa/` نسخة مبنية من تطبيق الويب التقدّمي الذي سبق الكشك (React 19 · TypeScript · Vite · Tailwind CSS 4 · Framer Motion · Workbox)، وكانت منشورة على [Netlify](https://clever-panda-42b0e5.netlify.app). أُلغي هذا المسار في أغسطس 2026 لصالح الكشك، وبقي منه نظام الهوية البصرية. الشيفرة المصدرية الأصلية غير متوفرة، لذلك يُحفظ هنا ملف البناء النهائي للمرجعية فقط.

## الحقوق

عمل مُنجز لصالح عميل. © 2026 أنس النابلسي — جميع الحقوق محفوظة. اسم «صيدلية مهند» وهويتها ملك لأصحابها. انظر [LICENSE](LICENSE).

</div>

---

## English summary

A portrait 43″ touch-kiosk application for a skincare-and-nutrition pharmacy in Damascus, Syria. The screen runs as digital signage (**attract mode**) most of the time; on touch it becomes an **interactive catalog** where customers browse products, check availability, and order by scanning a QR code that opens the pharmacy's WhatsApp.

| Folder | Description | Status |
| --- | --- | --- |
| `kiosk-app/` | Full kiosk app — attract mode, interactive catalog, hidden service screen | **Current product** |
| `attract-mode/` | Stand-alone attract-mode prototype | Prototype |
| `docs/architecture.html` | Architecture & design document (Arabic, 31 sections + sources) | v2 · Aug 2026 |
| `prototype-pwa/` | Compiled build of the earlier PWA prototype | Archived (source not available) |

**Highlights:** millimetre-based design system (`--mm`) that keeps physical sizes constant on any panel resolution · dependency-free QR encoder written from scratch (byte mode, EC level M, versions 1–20) · Arabic/English with CSS logical properties · idle-session reset · four-corner hidden service screen · Google Sheets CSV sync with `localStorage` cache and offline fallback · feature flags that keep regulated pharmaceutical content out of the kiosk pending Ministry of Health approval.

**Run:** single self-contained HTML file, no build step — open `kiosk-app/index.html` or serve the repo with `python3 -m http.server`.

**Deployment plan:** thin Kotlin `WebView` shell on the kiosk's Android OS, Device Owner + [lock task mode](https://developer.android.com/work/dpc/dedicated-devices/lock-task-mode).

Demo products and prices are illustrative only. Client work — © 2026 Anas Al-Nabulsi, all rights reserved.
