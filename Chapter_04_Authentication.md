# الفصل الرابع: المصادقة (Authentication)

## نظرة عامة على الفصل

يا جماعة، بعد ما فهمنا إزاي FortiGate بيتحكم في حركة المرور بالـ Firewall Policies، دلوقتي جه وقت نتكلم عن مين اللي مسموحله يعدي أصلاً! الفصل ده هيركز على المصادقة (Authentication)، يعني إزاي FortiGate بيتحقق من هوية المستخدمين قبل ما يسمحلهم بالوصول لموارد الشبكة.

هنتكلم عن أنواع المصادقة المختلفة اللي FortiGate بيدعمها، زي المستخدمين المحليين (Local Users)، والمصادقة عن طريق خوادم خارجية زي RADIUS و LDAP (اللي ممكن تكون Active Directory). كمان، هنتعمق في المصادقة الثنائية (Two-Factor Authentication - 2FA) باستخدام FortiToken، ودي حاجة مهمة جداً لزيادة الأمان. وهنشوف إزاي نربط المستخدمين دول بسياسات جدار الحماية عشان نتحكم في صلاحياتهم.

الفصل ده مهم جداً عشان تأمن الوصول لشبكتك، وتضمن إن اللي بيدخلها هو بس اللي مسموحله يدخل. يلا بينا نشوف إزاي FortiGate بيعمل كل ده!

## 4.1 المستخدمون والمجموعات المحلية (Local Users and Groups)

### 4.1.1 مفهوم المستخدمين المحليين

المستخدمون المحليون (Local Users) هم حسابات مستخدمين بيتم إنشاؤها وتخزينها مباشرة على جهاز FortiGate نفسه. دي طريقة بسيطة للمصادقة، ومناسبة للشبكات الصغيرة أو للمستخدمين الإداريين.

**متى نستخدم المستخدمين المحليين؟**
-   للمسؤولين اللي بيديروا FortiGate.
-   لعدد قليل من المستخدمين اللي بيحتاجوا وصول خاص (مثلاً: مستخدمي VPN).
-   في البيئات اللي مفيش فيها خادم مصادقة مركزي (زي Active Directory).

### 4.1.2 تكوين مستخدم محلي (Local User)

**عبر GUI:**
1.  انتقل إلى **(User & Authentication) -> (User Definition)**.
2.  انقر على **(Create New)**.
3.  **Type:** `Local User`.
4.  **User Name:** اسم المستخدم (مثلاً: `vpn_user`).
5.  **Password:** كلمة المرور.
6.  ممكن تحدد صلاحيات معينة للمستخدم ده (مثلاً: وصول VPN فقط).

**عبر CLI:**
```
config user local
    edit "vpn_user"
        set type password
        set passwd "StrongPassword123!"
    next
end
```

### 4.1.3 تكوين مجموعة مستخدمين محلية (Local User Group)

مجموعات المستخدمين بتسهل إدارة الصلاحيات، بدل ما تطبق سياسة على كل مستخدم لوحده، ممكن تطبقها على مجموعة.

**عبر GUI:**
1.  انتقل إلى **(User & Authentication) -> (User Groups)**.
2.  انقر على **(Create New)**.
3.  **Name:** اسم المجموعة (مثلاً: `VPN_Users_Group`).
4.  **Members:** ضيف المستخدمين المحليين اللي أنشأتهم للمجموعة دي.

**عبر CLI:**
```
config user group
    edit "VPN_Users_Group"
        set member "vpn_user"
    next
end
```

**مقارنة بـ Cisco:**

في Cisco، ممكن تعمل Local Users على الـ Router أو الـ ASA، وتستخدمهم للمصادقة. المفهوم واحد، لكن في الشركات الكبيرة بنفضل نستخدم خوادم مصادقة مركزية زي Active Directory أو RADIUS.

## 4.2 المصادقة عن طريق خوادم خارجية (Remote Authentication)

في الشبكات الكبيرة، بيكون عندنا عدد كبير من المستخدمين، وإدارة حساباتهم على كل جهاز لوحده بتكون مستحيلة. عشان كده بنستخدم خوادم مصادقة مركزية.

### 4.2.1 المصادقة باستخدام RADIUS

RADIUS (Remote Authentication Dial-In User Service) هو بروتوكول مصادقة مركزي شائع جداً. FortiGate بيشتغل كـ RADIUS Client، وبيبعت طلبات المصادقة لخادم RADIUS (زي Microsoft NPS أو Cisco ISE).

**مراحل المصادقة باستخدام RADIUS (مع FortiGate كعميل):**
1.  **المستخدم بيحاول يدخل:** أنت بتحاول توصل لحاجة محمية على الشبكة (زي VPN).
2.  **الـ FortiGate بيطلب منك إثبات هوية:** FortiGate بيستقبل طلبك، وبيبعت اسم المستخدم وكلمة السر لخادم الـ RADIUS.
3.  **خادم الـ RADIUS بيراجع الأوراق:** الـ RADIUS بيتحقق من اسم المستخدم وكلمة السر في قاعدة بياناته (ممكن تكون Active Directory). لو صح، بيبعت رد إيجابي لـ FortiGate، ولو غلط، بيبعت رد سلبي.
4.  **الـ FortiGate بياخد القرار:** بناءً على رد الـ RADIUS، FortiGate بيسمحلك بالدخول أو بيرفض طلبك.

**السر المشترك (Shared Secret):**

عشان FortiGate وخادم الـ RADIUS يثقوا في بعض، لازم يكون بينهم "سر مشترك" (Shared Secret). ده عبارة عن كلمة سر طويلة ومعقدة بتتكون على الجهازين. أي رسالة بتتبعت بين FortiGate والـ RADIUS بتكون متوقعة بالسر ده، عشان نتأكد إن مفيش حد في النص بيحاول يزور الرسائل.

**تكوين خادم RADIUS على FortiGate:**

**عبر GUI:**
1.  انتقل إلى **(User & Authentication) -> (RADIUS Servers)**.
2.  انقر على **(Create New)**.
3.  **Name:** اسم الخادم (مثلاً: `My_RADIUS_Server`).
4.  **Primary Server IP/Name:** عنوان IP لخادم الـ RADIUS.
5.  **Secret:** السر المشترك اللي متكون على خادم الـ RADIUS.

**عبر CLI:**
```
config user radius
    edit "My_RADIUS_Server"
        set server 10.0.0.10 # IP خادم RADIUS
        set secret "MySharedSecret123!"
        set auth-type auto
    next
end
```

**مقارنة بـ Cisco:**

في Cisco، بنستخدم بروتوكول RADIUS بشكل واسع للمصادقة المركزية، خاصة مع Cisco ISE (Identity Services Engine) اللي بيشتغل كخادم RADIUS. نفس مفهوم الـ Shared Secret موجود وضروري لتأمين الاتصال.

### 4.2.2 المصادقة باستخدام LDAP

LDAP (Lightweight Directory Access Protocol) هو بروتوكول بيستخدم للوصول لقواعد بيانات الدليل (Directory Services) زي Microsoft Active Directory. FortiGate ممكن يتصل بـ Active Directory مباشرة عن طريق LDAP عشان يتحقق من هوية المستخدمين.

**تكوين خادم LDAP على FortiGate:**

**عبر GUI:**
1.  انتقل إلى **(User & Authentication) -> (LDAP Servers)**.
2.  انقر على **(Create New)**.
3.  **Name:** اسم الخادم (مثلاً: `My_AD_Server`).
4.  **Server IP/Name:** عنوان IP لـ Domain Controller.
5.  **Common Name Identifier:** `sAMAccountName` (لـ Active Directory).
6.  **Distinguished Name:** الـ DN اللي هيبدأ منه البحث عن المستخدمين (مثلاً: `DC=example,DC=com`).
7.  **Bind Type:** `Regular`.
8.  **User DN:** اسم مستخدم ليه صلاحية البحث في الـ Active Directory (مثلاً: `CN=FortiGate_Bind,CN=Users,DC=example,DC=com`).
9.  **Password:** كلمة مرور المستخدم ده.

**عبر CLI:**
```
config user ldap
    edit "My_AD_Server"
        set server "10.0.0.20"
        set cnid "sAMAccountName"
        set dn "DC=example,DC=com"
        set type regular
        set username "FortiGate_Bind"
        set password "BindPassword123!"
    next
end
```

**مقارنة بـ Cisco:**

Cisco أيضاً بتدعم المصادقة باستخدام LDAP مع Active Directory، وده بيتم تكوينه على أجهزة زي ASA أو Router أو من خلال Cisco ISE.

## 4.3 المصادقة الثنائية (Two-Factor Authentication - 2FA)

### 4.3.1 مفهوم 2FA و FortiToken

المصادقة الثنائية (2FA) بتضيف طبقة أمان إضافية للمصادقة. بدل ما تعتمد على كلمة سر بس، بتطلب من المستخدم حاجة تانية يمتلكها (زي موبايل عليه تطبيق FortiToken) أو حاجة تكون جزء منه (زي بصمة الإصبع).

FortiToken هو حل Fortinet للمصادقة الثنائية، بيولد أكواد مرور لمرة واحدة (One-Time Passwords - OTP) على تطبيق على الموبايل أو جهاز مادي (Hardware Token).

**تكوين FortiToken:**

**الخطوة 1: تفعيل FortiToken على المستخدم (GUI):**
1.  انتقل إلى **(User & Authentication) -> (User Definition)**.
2.  اختر المستخدم اللي عايز تفعل عليه 2FA.
3.  في قسم **(Two-Factor Authentication)**، فعل `Enable Two-Factor Authentication`.
4.  اختر `FortiToken Mobile` أو `FortiToken Hardware`.
5.  لو FortiToken Mobile، هيظهرلك QR Code عشان المستخدم يعمل Scan ليه من التطبيق.

**عبر CLI:**
```
config user local
    edit "vpn_user"
        set two-factor fortitoken
        set fortitoken "FTKMOBXXXXXXXXXX" # Serial Number بتاع التوكن
    next
end
```

**مقارنة بـ Cisco:**

Cisco بتوفر حلول 2FA من خلال Cisco Duo Security، أو التكامل مع خوادم RADIUS اللي بتدعم 2FA. المفهوم واحد: إضافة عامل مصادقة تاني لزيادة الأمان.

## 4.4 مجموعات المستخدمين وسياسات المصادقة (User Groups and Authentication Policies)

### 4.4.1 ربط المستخدمين بسياسات جدار الحماية

بعد ما عرفنا المستخدمين (محليين أو من خوادم خارجية)، بنقدر نستخدمهم في سياسات جدار الحماية عشان نتحكم في وصولهم.

**عبر GUI:**
1.  انتقل إلى **(Policy & Objects) -> (Firewall Policy) -> (IPv4 Policy)**.
2.  انقر على **(Create New)** أو عدّل سياسة موجودة.
3.  في قسم **(Source)**، بدل ما تختار `all`، اختار `User Group` أو `User`، واختار المجموعة أو المستخدم اللي عايز تطبق عليه السياسة.
4.  لما المستخدم ده يحاول يعدي من السياسة دي، FortiGate هيطلب منه المصادقة.

**عبر CLI:**
```
config firewall policy
    edit <policy_id>
        set srcintf "lan"
        set dstintf "wan1"
        set srcaddr "all"
        set dstaddr "all"
        set groups "VPN_Users_Group" # ربط السياسة بمجموعة المستخدمين
        set service "HTTP" "HTTPS"
        set action accept
        set nat enable
    next
end
```

### 4.4.2 سياسات المصادقة (Authentication Policies)

FortiGate بيسمحلك تعمل سياسات مصادقة منفصلة عشان تتحكم في متى وأين يتم طلب المصادقة من المستخدمين.

**عبر GUI:**
1.  انتقل إلى **(Policy & Objects) -> (Authentication Policy)**.
2.  انقر على **(Create New)**.
3.  حدد الـ Source Interface, Source Address, Destination Interface, Destination Address, Service.
4.  **Action:** `Authenticate`.
5.  **User Group:** اختار مجموعة المستخدمين اللي عايزهم يعملوا مصادقة.

**مقارنة بـ Cisco:**

في Cisco، بنستخدم AAA (Authentication, Authorization, Accounting) عشان نتحكم في الوصول للموارد. ممكن نستخدم Access-lists مع AAA، أو نستخدم Cisco ISE لفرض سياسات وصول معقدة بناءً على هوية المستخدم.

## 4.5 تكامل SAML SSO (Single Sign-On)

### 4.5.1 مفهوم SAML SSO

SAML (Security Assertion Markup Language) هو معيار مفتوح لتبادل بيانات المصادقة والترخيص بين موفر الهوية (Identity Provider - IdP) وموفر الخدمة (Service Provider - SP). الـ SSO (Single Sign-On) بيسمح للمستخدم يسجل دخول مرة واحدة بس، ويقدر يوصل لأكتر من تطبيق أو خدمة من غير ما يسجل دخول تاني.

FortiGate ممكن يشتغل كـ Service Provider، ويتكامل مع IdP زي Azure AD أو Okta عشان يوفر تجربة SSO للمستخدمين.

**مراحل SAML SSO:**
1.  **المستخدم بيحاول يوصل لـ FortiGate (SP).**
2.  **FortiGate بيوجه المستخدم لـ IdP:** FortiGate بيعرف إن المستخدم ده لازم يعمل مصادقة عن طريق SAML، فبيبعته لـ IdP.
3.  **المستخدم بيعمل مصادقة مع IdP:** المستخدم بيدخل بيانات اعتماده (اسم المستخدم وكلمة السر) على صفحة الـ IdP.
4.  **IdP بيبعت SAML Assertion لـ FortiGate:** لو المصادقة نجحت، الـ IdP بيبعت رسالة مشفرة (SAML Assertion) لـ FortiGate، بتقول إن المستخدم ده تم التحقق من هويته.
5.  **FortiGate بيسمح للمستخدم بالدخول:** FortiGate بيتحقق من الـ SAML Assertion، وبيسمح للمستخدم بالوصول للموارد المطلوبة.

### 4.5.2 تكوين SAML SSO على FortiGate

التكوين معقد شوية وبيحتاج إعدادات على FortiGate وعلى الـ IdP. الخطوات الأساسية:

**الخطوة 1: تكوين IdP على FortiGate:**

**عبر GUI:**
1.  انتقل إلى **(User & Authentication) -> (SAML Providers)**.
2.  انقر على **(Create New)**.
3.  أدخل معلومات الـ IdP (URL, Certificate).

**الخطوة 2: تكوين FortiGate كـ SP على الـ IdP:**

هتحتاج تدخل معلومات FortiGate (SP Entity ID, Assertion Consumer Service URL) على الـ IdP.

**الخطوة 3: ربط SAML User Group بسياسة جدار الحماية:**

هتعمل User Group من نوع SAML وتضيفها لسياسة جدار الحماية زي ما عملنا مع باقي أنواع المستخدمين.

**مقارنة بـ Cisco:**

Cisco أيضاً بتدعم SAML SSO بشكل واسع في منتجاتها المختلفة (زي Cisco ASA, Cisco ISE, Cisco Duo) للتكامل مع IdPs زي Azure AD و Okta.

## 4.6 إدارة الضيوف (Guest Management)

### 4.6.1 مفهوم إدارة الضيوف

إدارة الضيوف هي طريقة لتوفير وصول مؤقت للإنترنت للزوار أو الضيوف في شبكتك، مع الحفاظ على أمان شبكتك الداخلية. FortiGate بيوفر بوابات مصادقة للضيوف (Guest Portals) تسمح للضيوف بالتسجيل والدخول.

**ميزات إدارة الضيوف:**
-   **صفحة تسجيل دخول مخصصة (Customizable Login Page):** ممكن تعمل صفحة تسجيل دخول خاصة بيك.
-   **مصادقة ذاتية (Self-Registration):** الضيوف بيقدروا يسجلوا نفسهم.
-   **وصول مؤقت (Temporary Access):** تحديد مدة زمنية للوصول.
-   **عزل الضيوف (Guest Isolation):** عزل شبكة الضيوف عن شبكة الشركة الداخلية.

### 4.6.2 تكوين Guest Portal

**الخطوة 1: إنشاء Guest User Group:**

**عبر GUI:**
1.  انتقل إلى **(User & Authentication) -> (User Groups)**.
2.  انقر على **(Create New)**.
3.  **Name:** `Guest_Users`.
4.  **Type:** `Guest`.

**الخطوة 2: تكوين Captive Portal على واجهة:**

**عبر GUI:**
1.  انتقل إلى **(Network) -> (Interfaces)**.
2.  اختر الواجهة اللي عليها شبكة الضيوف (مثلاً: `guest_wifi`).
3.  في قسم **(Admission Control)**، فعل `Enable Captive Portal`.
4.  **Authentication Method:** `Local` أو `External`.
5.  **User Group:** اختار `Guest_Users`.

**الخطوة 3: إنشاء Firewall Policy لشبكة الضيوف:**

هتعمل سياسة تسمح لـ `Guest_Users` بالوصول للإنترنت فقط، مع تطبيق Security Profiles المناسبة.

**مقارنة بـ Cisco:**

Cisco أيضاً بتوفر حلول Guest Access من خلال Cisco Wireless LAN Controllers (WLC) و Cisco ISE، مع إمكانية إنشاء Guest Portals مخصصة.

## الخلاصة

في الفصل ده، اتعلمنا إزاي FortiGate بيتعامل مع المصادقة، ودي خطوة أساسية في تأمين أي شبكة. عرفنا أنواع المستخدمين (محليين وخارجيين)، وإزاي نربط FortiGate بـ RADIUS و LDAP عشان نستخدم حسابات المستخدمين الموجودة عندنا.

الأهم من ده كله، إننا فهمنا قوة المصادقة الثنائية (2FA) باستخدام FortiToken، وإزاي بتضيف طبقة أمان إضافية. كمان، عرفنا إزاي نستخدم مجموعات المستخدمين في سياسات جدار الحماية، وإزاي FortiGate بيدعم SAML SSO وإدارة الضيوف.

كل دي ميزات قوية بتخلي FortiGate حل أمني متكامل. دلوقتي أنت جاهز إنك تدخل في عالم الـ VPNs، اللي هيوصلك بشبكتك من أي مكان في العالم بأمان. يلا بينا على الفصل اللي جاي!

---

*تأليف: Manus AI*
*تاريخ الإنشاء: ديسمبر 2024*

