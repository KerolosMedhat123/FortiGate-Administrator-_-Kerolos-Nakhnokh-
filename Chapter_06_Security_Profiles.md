# الفصل السادس: ملفات تعريف الأمان (Security Profiles - UTM Features)

## نظرة عامة على الفصل

يا جماعة، بعد ما فهمنا إزاي FortiGate بيتحكم في حركة المرور وبيعمل مصادقة للمستخدمين وبيعمل VPNs، دلوقتي جه وقت نتكلم عن الأسلحة الفتاكة اللي FortiGate بيستخدمها عشان يحارب التهديدات الأمنية. الفصل ده هيركز على ملفات تعريف الأمان (Security Profiles)، أو اللي بنسميها ميزات الـ UTM (Unified Threat Management).

دي الميزات اللي بتخلي FortiGate مش مجرد جدار حماية عادي، دي اللي بتخليه جدار حماية من الجيل التالي (NGFW). هنتكلم بالتفصيل عن كل ميزة: مكافحة الفيروسات (Antivirus)، نظام منع التسلل (IPS)، تصفية الويب (Web Filtering)، التحكم في التطبيقات (Application Control)، ومنع تسرب البيانات (DLP). هنشرح كل واحدة بتشتغل إزاي، وإزاي تكونها، وإزاي تربطها بسياسات جدار الحماية عشان توفر حماية متكاملة.

الفصل ده هو اللي هيخليك تستفيد من قوة FortiGate الحقيقية، وتأمن شبكتك من كل أنواع التهديدات. يلا بينا نخش في التفاصيل!

## 6.1 مقدمة إلى Security Profiles

### 6.1.1 ما هي Security Profiles؟

Security Profiles هي مجموعة من الميزات الأمنية المتقدمة اللي FortiGate بيطبقها على حركة المرور اللي بتعدي من خلاله. كل ميزة ليها ملف تعريف (Profile) خاص بيها، بتقدر تكونه وتخصصه حسب احتياجاتك. بعد كده، بتربط الـ Profiles دي بسياسات جدار الحماية (Firewall Policies).

**المبدأ الأساسي:**

لما حركة المرور تطابق سياسة جدار حماية معينة، FortiGate بيبص على الـ Security Profiles اللي مربوطة بالسياسة دي، وبيطبقها على حركة المرور. ده بيوفر حماية متعددة الطبقات (Layered Security)، يعني لو تهديد عدى من طبقة، ممكن يتقفش في الطبقة اللي بعدها.

### 6.1.2 أنواع Security Profiles

-   **Antivirus:** بيكتشف ويمنع الفيروسات والبرامج الضارة.
-   **Intrusion Prevention System (IPS):** بيكتشف ويمنع محاولات الاختراق والهجمات.
-   **Web Filter:** بيتحكم في تصفح الويب ويمنع الوصول للمواقع الضارة أو غير المرغوب فيها.
-   **Application Control:** بيتعرف على التطبيقات (حتى لو بتستخدم بورتات غير قياسية) ويتحكم فيها.
-   **Data Leakage Prevention (DLP):** بيمنع تسرب البيانات الحساسة خارج الشبكة.
-   **SSL/SSH Inspection:** بيفحص حركة المرور المشفرة (HTTPS, SSH) عشان يكتشف التهديدات المخفية جواها.

## 6.2 مكافحة الفيروسات (Antivirus)

### 6.2.1 مفهوم Antivirus

ميزة الـ Antivirus في FortiGate بتفحص الملفات اللي بتعدي من خلاله (زي الملفات اللي بتتحمل من الإنترنت أو اللي بتتبعت في الإيميل) عشان تكتشف الفيروسات والبرامج الضارة. بتعتمد على قاعدة بيانات من توقيعات الفيروسات (Virus Signatures) اللي بيتم تحديثها باستمرار من FortiGuard Labs.

### 6.2.2 تكوين Antivirus Profile

**عبر GUI:**
1.  انتقل إلى **(Security Profiles) -> (Antivirus)**.
2.  انقر على **(Create New)** أو عدّل الـ `default` Profile.
3.  **Scan Mode:**
    -   **Quick:** بيفحص الملفات بسرعة باستخدام توقيعات الفيروسات فقط.
    -   **Full:** بيفحص الملفات بشكل أعمق، وممكن يرسل الملفات المشبوهة لـ FortiSandbox لتحليلها.
4.  **Protocols:** حدد البروتوكولات اللي عايز تفحصها (HTTP, FTP, SMTP, POP3, IMAP).
5.  **Action:** حدد الإجراء اللي هيتاخد لو لقى فيروس (Block, Monitor).

**عبر CLI:**
```
config antivirus profile
    edit "my_av_profile"
        set scan-mode full
        config http
            set options scan
        end
        config ftp
            set options scan
        end
    next
end
```

### 6.2.3 ربط Antivirus Profile بسياسة جدار الحماية

**عبر GUI:**
1.  في سياسة جدار الحماية، في قسم **(Security Profiles)**، فعل `Antivirus` واختر الـ Profile اللي عملته.

**عبر CLI:**
```
config firewall policy
    edit <policy_id>
        set av-profile "my_av_profile"
    next
end
```

**مقارنة بـ Cisco AMP:**

Cisco Advanced Malware Protection (AMP) هو حل Cisco لمكافحة البرامج الضارة. بيشتغل بشكل مشابه لـ FortiGate Antivirus، لكنه بيوفر تحليل سلوكي متقدم وتتبع للملفات عبر الشبكة. FortiGate بيوفر نفس القدرات دي من خلال التكامل مع FortiSandbox.

## 6.3 نظام منع التسلل (Intrusion Prevention System - IPS)

### 6.3.1 مفهوم IPS

الـ IPS بيحمي الشبكة من الهجمات اللي بتستغل الثغرات في أنظمة التشغيل والتطبيقات. بيشتغل عن طريق مقارنة حركة المرور بقاعدة بيانات من توقيعات الهجمات المعروفة (IPS Signatures). لو لقى تطابق، بيمنع الهجوم فوراً.

### 6.3.2 تكوين IPS Sensor

**عبر GUI:**
1.  انتقل إلى **(Security Profiles) -> (Intrusion Prevention)**.
2.  انقر على **(Create New)** أو عدّل الـ `default` Sensor.
3.  **IPS Signatures:** ممكن تضيف توقيعات محددة أو تستخدم الفلاتر (Filters) عشان تفعل مجموعة من التوقيعات بناءً على الخطورة أو نوع الهجوم.
4.  **Action:** حدد الإجراء اللي هيتاخد لو لقى هجوم (Block, Monitor, Reset).

**عبر CLI:**
```
config ips sensor
    edit "my_ips_sensor"
        config entries
            edit 1
                set rule <signature_id>
                set action block
            next
        end
    next
end
```

### 6.3.3 ربط IPS Sensor بسياسة جدار الحماية

**عبر GUI:**
1.  في سياسة جدار الحماية، في قسم **(Security Profiles)**، فعل `IPS` واختر الـ Sensor اللي عملته.

**عبر CLI:**
```
config firewall policy
    edit <policy_id>
        set ips-sensor "my_ips_sensor"
    next
end
```

**مقارنة بـ Cisco Firepower IPS:**

Cisco Firepower NGFW بيوفر IPS قوي جداً، بيعتمد على قاعدة بيانات ضخمة من التوقيعات من Talos (فريق أبحاث التهديدات بتاع Cisco). المفهوم هو نفسه: مقارنة الترافيك بالتوقيعات ومنع الهجمات. FortiGate بيوفر نفس الوظيفة من خلال FortiGuard Labs.

## 6.4 تصفية الويب (Web Filtering)

### 6.4.1 مفهوم Web Filtering

Web Filtering بيسمحلك تتحكم في المواقع اللي المستخدمين بيقدروا يوصلولها. بيعتمد على قاعدة بيانات ضخمة بتصنف المواقع لفئات (Categories) زي (Social Media, News, Gambling, Malicious Sites). ممكن تسمح أو تمنع فئات معينة، أو تعمل تحذير للمستخدم قبل ما يدخل موقع معين.

### 6.4.2 تكوين Web Filter Profile

**عبر GUI:**
1.  انتقل إلى **(Security Profiles) -> (Web Filter)**.
2.  انقر على **(Create New)** أو عدّل الـ `default` Profile.
3.  **FortiGuard Category Based Filter:** هنا هتلاقي كل الفئات، ممكن تختار `Allow`, `Block`, `Monitor`, `Warning` لكل فئة.
4.  **Static URL Filter:** ممكن تضيف مواقع معينة وتسمح بيها أو تمنعها بغض النظر عن الفئة بتاعتها.

**عبر CLI:**
```
config webfilter profile
    edit "my_webfilter_profile"
        config ftgd-wf
            config filters
                edit 1
                    set category 26 # مثال: Social Networking
                    set action block
                next
            end
        end
    next
end
```

### 6.4.3 ربط Web Filter Profile بسياسة جدار الحماية

**عبر GUI:**
1.  في سياسة جدار الحماية، في قسم **(Security Profiles)**، فعل `Web Filter` واختر الـ Profile اللي عملته.

**عبر CLI:**
```
config firewall policy
    edit <policy_id>
        set webfilter-profile "my_webfilter_profile"
    next
end
```

**مقارنة بـ Cisco Umbrella:**

Cisco Umbrella هو حل سحابي لتصفية الويب وحماية DNS. بيوفر وظائف مشابهة لـ FortiGate Web Filter، لكنه بيشتغل على مستوى الـ DNS، وده بيخليه يقدر يحمي الأجهزة حتى لو كانت بره الشبكة. FortiGate بيوفر نفس الحماية دي من خلال FortiClient.

## 6.5 التحكم في التطبيقات (Application Control)

### 6.5.1 مفهوم Application Control

Application Control بيتعرف على التطبيقات اللي بتعدي من خلال FortiGate، حتى لو كانت بتستخدم بورتات غير قياسية أو بتحاول تتخفى. بيعتمد على قاعدة بيانات من توقيعات التطبيقات (Application Signatures). بعد ما يتعرف على التطبيق، ممكن تسمح بيه، أو تمنعه، أو تتحكم في استخدامه (مثلاً: تسمح بالفيسبوك بس تمنع الشات بتاعه).

### 6.5.2 تكوين Application Control Profile

**عبر GUI:**
1.  انتقل إلى **(Security Profiles) -> (Application Control)**.
2.  انقر على **(Create New)** أو عدّل الـ `default` Profile.
3.  **Categories:** ممكن تمنع فئات كاملة من التطبيقات (زي P2P, Proxy, Games).
4.  **Application Overrides:** ممكن تختار تطبيقات معينة وتسمح بيها أو تمنعها.

**عبر CLI:**
```
config application list
    edit "my_app_control_profile"
        config entries
            edit 1
                set application 15833 # مثال: Facebook
                set action block
            next
        end
    next
end
```

### 6.5.3 ربط Application Control Profile بسياسة جدار الحماية

**عبر GUI:**
1.  في سياسة جدار الحماية، في قسم **(Security Profiles)**، فعل `Application Control` واختر الـ Profile اللي عملته.

**عبر CLI:**
```
config firewall policy
    edit <policy_id>
        set application-list "my_app_control_profile"
    next
end
```

**مقارنة بـ Cisco Firepower Application Control:**

Cisco Firepower NGFW بيوفر Application Control قوي جداً، بيقدر يتعرف على آلاف التطبيقات ويتحكم فيها. المفهوم هو نفسه: التعرف على التطبيقات بناءً على التوقيعات وتطبيق سياسات عليها.

## 6.6 منع تسرب البيانات (Data Leakage Prevention - DLP)

### 6.6.1 مفهوم DLP

DLP بيحمي البيانات الحساسة (زي أرقام بطاقات الائتمان، أرقام الهوية، معلومات شخصية) من التسرب خارج الشبكة. بيشتغل عن طريق فحص حركة المرور بحثاً عن أنماط معينة (Patterns) بتدل على وجود بيانات حساسة.

### 6.6.2 تكوين DLP Sensor

**عبر GUI:**
1.  انتقل إلى **(Security Profiles) -> (Data Leak Prevention)**.
2.  انقر على **(Create New)** أو عدّل الـ `default` Sensor.
3.  **Rules:** ممكن تضيف قواعد (Rules) عشان تبحث عن أنماط معينة (زي `Credit-Card`, `SSN`) أو تعمل أنماط مخصصة.
4.  **Action:** حدد الإجراء اللي هيتاخد لو لقى بيانات حساسة (Block, Log Only).

**عبر CLI:**
```
config dlp sensor
    edit "my_dlp_sensor"
        config entries
            edit 1
                set filter-by file-type
                set file-type 1 # مثال: PDF
                set action block
            next
        end
    next
end
```

### 6.6.3 ربط DLP Sensor بسياسة جدار الحماية

**عبر GUI:**
1.  في سياسة جدار الحماية، في قسم **(Security Profiles)**، فعل `DLP` واختر الـ Sensor اللي عملته.

**عبر CLI:**
```
config firewall policy
    edit <policy_id>
        set dlp-sensor "my_dlp_sensor"
    next
end
```

**مقارنة بـ Cisco DLP:**

Cisco بتوفر حلول DLP من خلال منتجات مختلفة زي Cisco Email Security Appliance (ESA) و Web Security Appliance (WSA). المفهوم هو نفسه: فحص المحتوى بحثاً عن بيانات حساسة ومنع تسربها.

## 6.7 أوضاع الفحص (Inspection Modes)

FortiGate بيستخدم وضعين رئيسيين لفحص حركة المرور:

-   **Flow-based Inspection:**
    -   بيفحص حركة المرور أثناء مرورها (on-the-fly) من غير ما يخزن الملفات كلها.
    -   أسرع في الأداء، لكنه أقل دقة في اكتشاف بعض التهديدات المعقدة.
    -   مناسب لمعظم أنواع حركة المرور.

-   **Proxy-based Inspection:**
    -   بيشتغل كـ Proxy، يعني بيستقبل الملف كله، وبيفحصه، وبعدين بيبعته للوجهة.
    -   أبطأ في الأداء، لكنه بيوفر فحص أعمق وأكثر دقة.
    -   ضروري لبعض الميزات زي DLP و SSL Inspection.

**تحديد وضع الفحص في سياسة جدار الحماية (GUI):**
1.  في سياسة جدار الحماية، هتلاقي خيار **(Inspection Mode)**.
2.  اختر `Flow-based` أو `Proxy-based`.

**مقارنة بـ Cisco:**

Cisco Firepower NGFW بيستخدم تقنيات فحص مشابهة، بيقدر يفحص الترافيك أثناء مروره، وفي نفس الوقت بيقدر يعمل تحليل أعمق للملفات باستخدام AMP.

## الخلاصة

في الفصل ده، اتعمقنا في قلب قوة FortiGate الأمنية: الـ Security Profiles. عرفنا إزاي كل ميزة (Antivirus, IPS, Web Filter, Application Control, DLP) بتشتغل، وإزاي تكونها، وإزاي تربطها بسياسات جدار الحماية.

دلوقتي أنت فهمت إزاي تبني دفاع متعدد الطبقات، وإزاي تستفيد من كل إمكانيات FortiGate عشان تحمي شبكتك من كل أنواع التهديدات. لو قدرت تطبق اللي اتعلمته في الفصل ده، فأنت بقيت قادر على بناء حل أمني قوي ومتكامل لأي شبكة.

يلا بينا على الفصل اللي جاي عشان نتكلم عن إدارة النظام والتوافرية العالية (HA)، عشان نضمن إن FortiGate بتاعنا شغال دايماً ومستقر.

---

*تأليف: Manus AI*
*تاريخ الإنشاء: ديسمبر 2024*

