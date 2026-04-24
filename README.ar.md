# Pixel IMS: تفعيل VoLTE على أجهزة Tensor Pixel

> **اللغات:** [English](README.md) | [한국어](README.ko.md) | [العربية](README.ar.md)

## **ملاحظة:** قد لا تستمر التغييرات بعد إعادة التشغيل على الأجهزة التي تعمل بنظام Android 16 QPR2 Beta 3 أو أحدث
بسبب التدابير الأمنية المطبقة على نظام Android نيابة عن Google، قد تضطر إلى إعادة تطبيق التصحيح بعد كل عملية إعادة تشغيل. راجع قسم الأسئلة الشائعة في الأسفل لمزيد من التفاصيل.

## استكشاف الأخطاء وإصلاحها

راجع [هنا](https://github.com/kyujin-cho/pixel-volte-patch/blob/main/docs/troubleshooting.en.md).

## مقدمة

يصف هذا المستند كيفية تفعيل دعم VoLTE على أجهزة مختارة من Google Pixel باستخدام وظيفة Android الداخلية `telephony.ICarrierConfigLoader.overrideConfig()`. يمكن اعتبار هذا التصحيح طريقة لا تتطلب صلاحيات الروت (rootless) مقارنة بـ [voenabler](https://github.com/cigarzh/voenabler).

## شركات الاتصال المدعومة

### دعم من الدرجة الأولى

شركات الاتصال التي يمكن للمطور اختبار عمل التصحيح عليها فوراً:

- LG U+ (كوريا الجنوبية) والشركات الافتراضية (MVNOs) التابعة لها

### دعم من الدرجة الثانية

شركات الاتصال التي لا يمكن للمطور اختبارها ولكن تم الإبلاغ عن دعمها من قبل المجتمع. يرجى الرجوع إلى [الرابط](https://github.com/kyujin-cho/pixel-volte-patch/blob/main/docs/compatibility-chart.en.md) للحصول على القائمة الكاملة لشركات الاتصال.

## تطبيق التصحيح

### المتطلبات

- جهاز Pixel مزود بشريحة Google Tensor
  - Google Pixel 6
  - Google Pixel 6a
  - Google Pixel 6 Pro
  - Google Pixel 7
  - Google Pixel 7a
  - Google Pixel 7 Pro
  - Google Pixel 8
  - Google Pixel 8 Pro
  - Google Pixel Fold

#### اختياري (في حال تثبيت Shizuku باستخدام ADB)

- جهاز كمبيوتر يعمل بنظام Windows أو macOS أو Linux مع تثبيت [Android Platform Tools](https://developer.android.com/studio/command-line/adb)
- كابل USB-A إلى USB-C أو USB-C إلى USB-C لتوصيل جهاز Pixel بالكمبيوتر

### تثبيت Shizuku

يجعل تطبيق [Shizuku](https://shizuku.rikka.app/) من الممكن استدعاء واجهات برمجة تطبيقات (APIs) Android الداخلية بدون صلاحيات الروت من خلال إنشاء خدمة وكيلة مع مستخدم ADB.

1. قم بتثبيت [Shizuku](https://play.google.com/store/apps/details?id=moe.shizuku.privileged.api) على جهاز Pixel الذي تحاول تصحيحه.
   <br><img src="https://github.com/kyujin-cho/pixel-volte-patch/raw/main/assets/Screenshot_20230206-035249.png" width="300" />
2. افتح التطبيق المثبت.
   <br><img src="https://github.com/kyujin-cho/pixel-volte-patch/raw/main/assets/Screenshot_20230206-035312.png" width="300" />

### بدء Shizuku بدون كمبيوتر (يتطلب اتصال Wi-Fi)

1. اتبع [الدليل الرسمي لبدء Shizuku باستخدام تصحيح أخطاء اللاسلكي](https://shizuku.rikka.app/guide/setup/#start-via-wireless-debugging) دون الحاجة إلى أي كمبيوتر خارجي، وبعد ذلك يجب أن ترى شيئاً مثل "Shizuku is running" على هاتفك Pixel.
   <br><img src="https://github.com/kyujin-cho/pixel-volte-patch/raw/main/assets/Screenshot_20230206-035351.png" width="300" />
2. الآن انتقل إلى القسم التالي.

### بدء Shizuku باستخدام الكمبيوتر

1. قم بتوصيل هاتفك Pixel بالكمبيوتر باتباع [هذا الوصف](https://shizuku.rikka.app/guide/setup/#start-by-connecting-to-a-computer).
2. ابدأ خدمة Shizuku عن طريق تنفيذ الأمر `adb shell sh /sdcard/Android/data/moe.shizuku.privileged.api/start.sh`. يجب أن ترى شيئاً مثل "Shizuku is running" على هاتفك Pixel.
   <br><img src="https://github.com/kyujin-cho/pixel-volte-patch/raw/main/assets/Screenshot%202023-02-06%20at%203.54.00%20AM.png" width="500" />
   <br><img src="https://github.com/kyujin-cho/pixel-volte-patch/raw/main/assets/Screenshot_20230206-035351.png" width="300" />
3. الآن انتقل إلى القسم التالي.

### تثبيت تطبيق Pixel IMS
1. في الوقت الحالي، هناك طريقتان للحصول على التطبيق. اختر طريقتك المفضلة وقم بتثبيت التطبيق.
   - عبر [Play Store](https://play.google.com/store/apps/details?id=dev.bluehouse.enablevolte)
   - من [Github Releases](https://github.com/kyujin-cho/pixel-volte-patch/releases/download/1.3.1/dev.bluehouse.enablevolte.apk)، عن طريق تنزيل ملف APK
2. ابدأ التطبيق المثبت.
3. اضغط على "السماح طوال الوقت" (Allow all the time) عند رؤية المطالبة التي تطلب إذن Shizuku.
   <br><img src="https://github.com/kyujin-cho/pixel-volte-patch/raw/main/assets/Screenshot_20230208-235239.png" width="300" />
4. قم بتبديل خيار "Enable VoLTE" لتفعيل VoLTE.
   <br><img src="https://github.com/kyujin-cho/pixel-volte-patch/raw/main/assets/Screenshot_20230208-234343.png" width="300" />
5. أعد تشغيل هاتفك Pixel مرتين أو ثلاث مرات حتى ترى أن VoLTE يعمل.

### بناء التطبيق من المصدر
قم بتنزيل [ملف android.jar المصحح](https://github.com/Reginer/aosp-android-jar/raw/main/android-34/android.jar)، وضعه في المسار `$ANDROID_PATH/sdk/platforms/android-34` وابدأ التطوير كالمعتاد.

## الأسئلة الشائعة

### أين يمكنني إرسال الملاحظات؟

- تقارير الأخطاء وطلبات الميزات: [Issues](https://github.com/kyujin-cho/pixel-volte-patch)
- أي شيء آخر (بما في ذلك الأسئلة العامة): [Discussions](https://github.com/kyujin-cho/pixel-volte-patch/discussions)

### هل يعمل أيضاً على أي شركات اتصال أخرى غير LG U+؟

على مسؤوليتك الشخصية (AYOR). تم اختباره والتحقق من عمله فقط مع LG U+ والشركات الافتراضية (MVNOs) التي تستخدم شبكتها.

### كيف أعرف ما إذا كان VoLTE مفعلاً أم لا؟

تشير حالة IMS `Registered` في الصفحة الرئيسية إلى أن VoLTE مفعل.
<br><img src="https://github.com/kyujin-cho/pixel-volte-patch/raw/main/assets/Screenshot_20230208-234340.png" width="300" />

لمزيد من المعلومات، يمكنك الاستفادة من التطبيق الداخلي لـ Pixel. لفتحه:

1. افتح تطبيق الهاتف الافتراضي (Dialer) من هاتفك Pixel.
   <br><img src="https://github.com/kyujin-cho/pixel-volte-patch/raw/main/assets/Screenshot_20230206-035705.png" width="300" />
2. اطلب `*#*#4636#*#*`.
   <br><img src="https://github.com/kyujin-cho/pixel-volte-patch/raw/main/assets/Screenshot_20230206-035701.png" width="300" />
3. اضغط على قائمة "Phone information".
   <br><img src="https://github.com/kyujin-cho/pixel-volte-patch/raw/main/assets/Screenshot_20230206-035650.png" width="300" />
4. اضغط على أيقونة النقاط الثلاث في أعلى يمين الشاشة ثم اختر قائمة "IMS Service Status".
   <br><img src="https://github.com/kyujin-cho/pixel-volte-patch/raw/main/assets/Screenshot_20230206-030524.png" width="300" />
5. يجب أن ترى `IMS Registration: Registered` إذا تم كل شيء بنجاح.
   <br><img src="https://github.com/kyujin-cho/pixel-volte-patch/raw/main/assets/Screenshot_20230206-035645.png" width="300" />

### هل يجب علي القيام بذلك في كل مرة أقوم فيها بإعادة تشغيل الهاتف؟

- الأجهزة التي تعمل بنظام **Android 16 QPR2 Beta 3** أو أحدث: [نعم](https://github.com/kyujin-cho/pixel-volte-patch/issues/398).
- الأجهزة الأخرى: لا.

### هل يجب علي القيام بذلك بعد تحديث نظام جهازي Pixel؟

نعم.

### كيف تعمل هذه الأداة؟

هناك دالة تحقق تسمى `ImsManager.isVolteEnabledByPlatform(Context)`، والتي تحدد ما إذا كان VoLTE ممكناً لمجموعة الجهاز وشركة الاتصال الخاصة بك (المرجع: [googlesource.com](https://android.googlesource.com/platform/frameworks/opt/net/ims/+/002b204/src/java/com/android/ims/ImsManager.java)). المنطق المجرد لهذه الدالة هو:

1. التحقق مما إذا كانت خاصية النظام `persist.dbg.volte_avail_ovr` صحيحة (true)
   - إذا كانت نعم، ترجع true
     - هذه هي الطريقة التي يعمل بها voenabler
   - وإلا تستمر
2. التحقق مما إذا كان الجهاز يدعم VoLTE
   - إذا كان لا، ترجع false
   - وإلا تستمر
3. **التحقق مما إذا كانت شركة الاتصال تدعم VoLTE**
   - إذا كان لا، ترجع false
   - وإلا تستمر
4. التحقق مما إذا كانت شركة الاتصال تتطلب شريحة تدعم BGA لـ VoLTE
   - إذا كان لا، ترجع true
   - وإلا تستمر
5. التحقق مما إذا كان بت GBA نشطاً في EF IST
   - إذا كان نعم، ترجع true
   - إذا كان لا، ترجع false

يقوم هذا التصحيح بتغيير المنطق المكتوب بالخط العريض، عن طريق فرض حقن قيم التكوين كـ true بغض النظر عن تكوين شركة الاتصال.
