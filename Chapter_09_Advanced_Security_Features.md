# الفصل التاسع: ميزات الأمان المتقدمة (Advanced Security Features)

## نظرة عامة على الفصل

يا جماعة، بعد ما اتكلمنا عن التوجيه المتقدم والشبكات اللاسلكية، دلوقتي هنخش في قلب المعركة ضد التهديدات الحديثة: ميزات الأمان المتقدمة في FortiGate. الفصل ده هيوريك إزاي FortiGate بيقدر يحميك من الهجمات المعقدة اللي بتعدي من جدران الحماية التقليدية.

هنتكلم عن الـ SSL Inspection، ودي ميزة مهمة جداً عشان FortiGate يقدر يفحص الترافيك المشفر (زي HTTPS) ويكتشف التهديدات اللي ممكن تكون مستخبية جواه. كمان، هنتعمق في الـ Advanced Threat Protection (ATP) اللي بتشمل تقنيات زي الـ Sandbox عشان تكتشف البرامج الضارة الجديدة (Zero-day attacks) اللي محدش يعرفها. وهنشوف إزاي FortiGate بيقدر يتحكم في الوصول للشبكة بناءً على حالة الجهاز (Network Access Control - NAC) وإزاي بيقدر يعمل Bandwidth Control عشان يضمن إن التطبيقات المهمة ليها الأولوية.

الفصل ده هيخليك سابق بخطوة في عالم الأمن السيبراني، وهيوريك إزاي FortiGate بيوفر حماية شاملة ضد أحدث التهديدات. يلا بينا نخش في التفاصيل!

## 9.1 فحص SSL (SSL Inspection)

### 9.1.1 مفهوم SSL Inspection

معظم الترافيك على الإنترنت دلوقتي بقى مشفر باستخدام SSL/TLS (HTTPS). ده كويس للخصوصية، لكنه بيخلي اكتشاف التهديدات صعب على جدران الحماية التقليدية. الـ SSL Inspection (أو SSL Decryption) بتسمح للـ FortiGate إنه يفك تشفير الترافيك ده، يفحصه بحثاً عن تهديدات، وبعدين يشفره تاني ويبعته للوجهة.

**ليه بنحتاج SSL Inspection؟**
-   **اكتشاف التهديدات المخفية:** الفيروسات والبرامج الضارة ممكن تيجي جوه الترافيك المشفر.
-   **تطبيق السياسات:** الـ Web Filtering والـ Application Control والـ DLP مش هتشتغل صح على الترافيك المشفر من غير SSL Inspection.

**كيف يعمل SSL Inspection؟**

FortiGate بيشتغل كـ `Man-in-the-Middle` (بشكل شرعي). لما جهازك بيحاول يتصل بموقع HTTPS، FortiGate بيعترض الاتصال، وبيولد شهادة SSL خاصة بيه للموقع ده، وبيبعتها لجهازك. جهازك بيثق في شهادة FortiGate (بعد ما تثبت الـ Certificate Authority بتاع FortiGate على أجهزتك)، وبعدين FortiGate بيعمل اتصال حقيقي بالموقع الأصلي. كده FortiGate بيقدر يشوف الترافيك المشفر ويفحصه.

### 9.1.2 أنواع SSL Inspection

-   **Certificate Inspection (Flow-based):**
    -   بيفحص الشهادات بس، مش بيفك تشفير الترافيك كله.
    -   أسرع، لكنه أقل أماناً.
    -   مناسب للبيئات اللي فيها مشاكل في تثبيت الشهادات على الأجهزة.

-   **Deep Inspection (Proxy-based):**
    -   بيفك تشفير الترافيك كله ويفحصه بعمق.
    -   أكثر أماناً، لكنه بيحتاج تثبيت شهادة الـ FortiGate CA على كل الأجهزة في الشبكة.
    -   بيستهلك موارد أكتر من FortiGate.

### 9.1.3 تكوين SSL Inspection Profile

**عبر GUI:**
1.  انتقل إلى **(Security Profiles) -> (SSL/SSH Inspection)**.
2.  انقر على **(Create New)** أو عدّل الـ `deep-inspection` Profile.
3.  **Inspection Method:** اختار `Deep Inspection`.
4.  **Certificate Inspection Options:** ممكن تستثني بعض المواقع من الـ Inspection (زي المواقع البنكية).

**عبر CLI:**
```
config firewall ssl-ssh-profile
    edit "deep-inspection-profile"
        set inspect-all enable
        set ssl-flow-override disable
        set use-ssl-server-cert disable
        set untrusted-can-block enable
        set expired-can-block enable
        set revoked-can-block enable
        set unknown-can-block enable
        set ports 443
    next
end
```

### 9.1.4 ربط SSL Inspection Profile بسياسة جدار الحماية

**عبر GUI:**
1.  في سياسة جدار الحماية، في قسم **(Security Profiles)**، فعل `SSL/SSH Inspection` واختر الـ Profile اللي عملته.

**عبر CLI:**
```
config firewall policy
    edit <policy_id>
        set ssl-ssh-profile "deep-inspection-profile"
    next
end
```

**مقارنة بـ Cisco Firepower SSL Decryption:**

Cisco Firepower NGFW بيوفر نفس إمكانيات فك تشفير SSL، وبيستخدم نفس المفهوم بتاع الـ Man-in-the-Middle. بيحتاج تثبيت شهادة الـ Firepower CA على الأجهزة عشان يشتغل صح. المفهوم هو نفسه: فحص الترافيك المشفر لزيادة الأمان.

## 9.2 الحماية المتقدمة من التهديدات (Advanced Threat Protection - ATP)

### 9.2.1 مفهوم ATP

ATP هي مجموعة من التقنيات اللي بتشتغل مع بعض عشان تحمي من التهديدات المتقدمة والمعقدة، خصوصاً هجمات الـ Zero-day (الهجمات اللي محدش يعرفها لسه). FortiGate بيحقق ده من خلال التكامل مع FortiSandbox.

**مكونات ATP:**
-   **Sandbox:** بيئة معزولة بيتم فيها تشغيل الملفات المشبوهة عشان نشوف سلوكها. لو الملف حاول يعمل حاجة غريبة (زي إنه يحاول يغير في ملفات النظام أو يتصل بسيرفرات مشبوهة)، الـ Sandbox بيكتشفه كبرنامج ضار.
-   **Anti-Malware:** بيستخدم توقيعات الفيروسات المعروفة.
-   **Threat Intelligence:** بيستخدم معلومات عن التهديدات الجديدة من FortiGuard Labs.

### 9.2.2 تكوين FortiSandbox Integration

**عبر GUI:**
1.  انتقل إلى **(Security Fabric) -> (Fabric Connectors)**.
2.  انقر على **(Create New)** واختار `FortiSandbox`.
3.  أدخل عنوان IP الـ FortiSandbox وكلمة السر.
4.  فعل الـ `File Filter` عشان FortiGate يبعت الملفات المشبوهة لـ FortiSandbox.

**عبر CLI:**
```
config system fortisandbox
    set status enable
    set server "10.0.0.250" # IP FortiSandbox
    set enc-algorithm high
    set sandbox-type fortisandbox
    set upload-option all
end
```

### 9.2.3 ربط Sandbox Profile بسياسة جدار الحماية

**عبر GUI:**
1.  في سياسة جدار الحماية، في قسم **(Security Profiles)**، فعل `Sandbox Inspection` واختر الـ Profile اللي عملته.

**عبر CLI:**
```
config firewall policy
    edit <policy_id>
        set av-profile "my_av_profile"
        set sandbox-profile "default"
    next
end
```

**مقارنة بـ Cisco Threat Defense (Firepower + AMP + Threat Grid):**

Cisco بتوفر حل ATP متكامل من خلال Firepower NGFW مع AMP (Advanced Malware Protection) و Threat Grid (حل الـ Sandbox بتاع Cisco). المفهوم هو نفسه: اكتشاف التهديدات المعروفة وغير المعروفة من خلال تحليل السلوك في بيئة معزولة.

## 9.3 التحكم في الوصول للشبكة (Network Access Control - NAC)

### 9.3.1 مفهوم NAC

NAC بيسمحلك تتحكم في الأجهزة اللي بتوصل لشبكتك بناءً على حالتها الأمنية. يعني ممكن تقول: "لو الجهاز ده عليه Antivirus شغال ومتحدث، اسمحله بالوصول الكامل. لو مش عليه، خليه يوصل للإنترنت بس، أو امنعه خالص". FortiGate بيحقق ده من خلال التكامل مع FortiNAC.

**فوائد NAC:**
-   **تحسين الأمان:** منع الأجهزة غير المتوافقة أو المصابة من الوصول للشبكة.
-   **الامتثال (Compliance):** ضمان أن الأجهزة تلتزم بالسياسات الأمنية للشركة.
-   **رؤية شاملة:** معرفة كل الأجهزة المتصلة بالشبكة وحالتها.

### 9.3.2 تكوين FortiNAC Integration

**عبر GUI:**
1.  انتقل إلى **(Security Fabric) -> (Fabric Connectors)**.
2.  انقر على **(Create New)** واختار `FortiNAC`.
3.  أدخل عنوان IP الـ FortiNAC وكلمة السر.

**عبر CLI:**
```
config system fortinac
    edit "FortiNAC_Server"
        set server "10.0.0.260"
        set username "fortinac_user"
        set password "FortiNACPass!"
    next
end
```

### 9.3.3 ربط NAC بسياسات جدار الحماية

بعد التكامل، FortiGate بيقدر يستخدم معلومات الـ NAC عشان يطبق سياسات جدار الحماية بناءً على حالة الجهاز. يعني ممكن تعمل سياسة تسمح لأجهزة معينة بالوصول بناءً على الـ Health Status بتاعها من FortiNAC.

**مقارنة بـ Cisco ISE:**

Cisco Identity Services Engine (ISE) هو حل Cisco للـ NAC. بيوفر نفس الوظائف: المصادقة، الترخيص، وتقييم حالة الجهاز. بيقدر يدمج مع أجهزة Cisco المختلفة (Switches, Routers, Wireless APs) عشان يفرض سياسات الوصول بناءً على هوية المستخدم وحالة الجهاز.

## 9.4 التحكم في عرض النطاق الترددي (Bandwidth Control)

### 9.4.1 مفهوم Bandwidth Control

Bandwidth Control (أو Quality of Service - QoS) بيسمحلك تتحكم في كمية عرض النطاق الترددي (Bandwidth) اللي التطبيقات أو المستخدمين بيستخدموها. ده بيضمن إن التطبيقات الحيوية (زي الـ VoIP أو الفيديو كونفرنس) ليها الأولوية، وإن التطبيقات اللي مش مهمة (زي تحميل الملفات الكبيرة) متستهلكش كل الـ Bandwidth.

### 9.4.2 تكوين Traffic Shapers

**عبر GUI:**
1.  انتقل إلى **(Policy & Objects) -> (Traffic Shapers)**.
2.  انقر على **(Create New)**.
3.  **Type:** `Shared` أو `Per-IP`.
4.  **Guaranteed Bandwidth:** أقل Bandwidth مضمون للترافيك ده.
5.  **Maximum Bandwidth:** أقصى Bandwidth ممكن الترافيك ده يستخدمه.

**عبر CLI:**
```
config firewall shaper traffic-shaper
    edit "High_Priority_Traffic"
        set per-ip-shaper disable
        set guaranteed-bandwidth 10000 # 10 Mbps
        set maximum-bandwidth 50000 # 50 Mbps
    next
end
```

### 9.4.3 ربط Traffic Shapers بسياسات جدار الحماية

**عبر GUI:**
1.  في سياسة جدار الحماية، في قسم **(Traffic Shaping)**، فعل `Traffic Shapers` واختر الـ Shaper اللي عملته للـ `Source` و `Destination`.

**عبر CLI:**
```
config firewall policy
    edit <policy_id>
        set traffic-shaper "High_Priority_Traffic"
        set traffic-shaper-reverse "High_Priority_Traffic"
    next
end
```

**مقارنة بـ Cisco QoS:**

Cisco عندها مجموعة واسعة من أدوات الـ QoS (Quality of Service) على Routers و Switches. بتستخدم مفاهيم زي Classification, Marking, Policing, Shaping, Queuing. المفهوم هو نفسه: التحكم في الترافيك عشان تضمن جودة الخدمة للتطبيقات الحيوية.

## الخلاصة

في الفصل ده، اتعمقنا في ميزات الأمان المتقدمة اللي بتخلي FortiGate في مصاف جدران الحماية من الجيل التالي. عرفنا إزاي الـ SSL Inspection بتكشف التهديدات المخفية في الترافيك المشفر، وإزاي الـ ATP بتحمي من هجمات الـ Zero-day من خلال الـ Sandbox.

كمان، فهمنا أهمية الـ NAC في التحكم في وصول الأجهزة للشبكة بناءً على حالتها الأمنية، وإزاي الـ Bandwidth Control بيضمن جودة الخدمة للتطبيقات الحيوية. كل دي ميزات بتخلي FortiGate حل أمني متكامل وقوي جداً.

دلوقتي أنت بقيت مسلح بالمعرفة اللازمة عشان تحمي شبكتك من أحدث وأعقد التهديدات. يلا بينا على الفصل اللي جاي عشان نتكلم عن استكشاف الأخطاء وإصلاحها (Troubleshooting) وأدوات التشخيص في FortiGate، عشان لو حصل أي مشكلة، تعرف تحلها بنفسك!

---

*تأليف: Manus AI*
*تاريخ الإنشاء: ديسمبر 2024*

