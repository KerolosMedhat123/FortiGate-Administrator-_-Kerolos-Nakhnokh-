# الفصل الثاني عشر: أمثلة عملية وسيناريوهات تطبيقية (Practical Examples and Scenarios)

## نظرة عامة على الفصل

يا جماعة، بعد ما خلصنا كل المواضيع النظرية والتكوينات الفردية، دلوقتي جه وقت التطبيق العملي! الفصل ده هو اللي هيجمع كل اللي اتعلمناه في الفصول اللي فاتت، وهنوريك إزاي كل ده بيشتغل مع بعضه في سيناريوهات حقيقية ممكن تقابلك في الشغل.

هنتكلم عن سيناريوهات شائعة زي تأمين فرع شركة (Branch Office Deployment)، وتأمين الوصول عن بُعد (Securing Remote Access) للموظفين، وتأمين تطبيقات الويب (Web Application Security) من الهجمات. في كل سيناريو، هنراجع المكونات اللي هنستخدمها من FortiGate، وإزاي نربطها ببعض عشان نحقق الهدف الأمني المطلوب.

الفصل ده هيخليك تشوف الصورة الكبيرة، وإزاي FortiGate بيقدم حلول متكاملة لمشاكل أمنية حقيقية. يلا بينا نخش في التفاصيل!

## 12.1 تأمين فرع شركة (Branch Office Deployment)

### 12.1.1 السيناريو

عندك شركة ليها مقر رئيسي (Head Office) وفروع (Branch Offices) في أماكن مختلفة. عايز تربط الفروع بالمقر الرئيسي بشكل آمن، وتوفر للفرع وصول للإنترنت محمي، وتتحكم في الترافيك اللي رايح جاي بين الفرع والمقر الرئيسي، وبين الفرع والإنترنت.

**المتطلبات:**
-   اتصال آمن بين الفرع والمقر الرئيسي.
-   وصول آمن للإنترنت من الفرع.
-   تطبيق سياسات أمنية موحدة على ترافيك الفرع.

### 12.1.2 الحل باستخدام FortiGate

**المكونات الرئيسية:**
-   **FortiGate في المقر الرئيسي:** هيكون الـ Hub في الـ VPN.
-   **FortiGate في الفرع:** هيكون الـ Spoke في الـ VPN.
-   **IPsec VPN Site-to-Site:** لربط الفرع بالمقر الرئيسي بشكل آمن.
-   **Firewall Policies:** للتحكم في الترافيك بين الفرع والمقر الرئيسي، وبين الفرع والإنترنت.
-   **Security Profiles:** لتأمين ترافيك الإنترنت (Antivirus, IPS, Web Filter, Application Control).

**خطوات التكوين (ملخص):**

1.  **تكوين IPsec VPN Site-to-Site:**
    -   على FortiGate المقر الرئيسي والفرع، كون IPsec VPN Site-to-Site زي ما اتعلمنا في الفصل الخامس. تأكد من أن الـ Phase 1 والـ Phase 2 متطابقين، وأن الـ Local/Remote Subnets معرفة بشكل صحيح.

2.  **تكوين Static Routes:**
    -   على FortiGate المقر الرئيسي، اعمل Static Route للشبكة الداخلية للفرع عبر الـ IPsec Tunnel.
    -   على FortiGate الفرع، اعمل Static Route للشبكة الداخلية للمقر الرئيسي عبر الـ IPsec Tunnel.

3.  **تكوين Firewall Policies:**
    -   **ترافيك الفرع للمقر الرئيسي:**
        -   **Source Interface:** `lan` (في الفرع).
        -   **Destination Interface:** الـ IPsec Tunnel.
        -   **Source:** شبكة الفرع الداخلية.
        -   **Destination:** شبكة المقر الرئيسي الداخلية.
        -   **Action:** `ACCEPT`.
        -   **NAT:** `Disable`.
    -   **ترافيك المقر الرئيسي للفرع:** (نفس السياسة بس بالعكس).
    -   **ترافيك الفرع للإنترنت:**
        -   **Source Interface:** `lan` (في الفرع).
        -   **Destination Interface:** `wan1`.
        -   **Source:** شبكة الفرع الداخلية.
        -   **Destination:** `all`.
        -   **Action:** `ACCEPT`.
        -   **NAT:** `Enable`.
        -   **Security Profiles:** فعل الـ Antivirus, IPS, Web Filter, Application Control.

4.  **تكوين Security Profiles:**
    -   على FortiGate الفرع، كون الـ Security Profiles المناسبة (Antivirus, IPS, Web Filter, Application Control) وطبقها على سياسة الإنترنت.

**مقارنة بـ Cisco:**

نفس السيناريو ممكن يتم باستخدام Cisco Routers أو ASA. هتستخدم IPsec VPN Site-to-Site، وهتكون Static Routes، وهتطبق Access-lists أو Firepower Policies لتأمين الترافيك. المفهوم هو نفسه، لكن أوامر التكوين بتختلف.

## 12.2 تأمين الوصول عن بُعد (Securing Remote Access)

### 12.2.1 السيناريو

الموظفين بيشتغلوا من البيت أو من أي مكان بره الشركة، ومحتاجين يوصلوا لموارد الشركة الداخلية (ملفات، تطبيقات، سيرفرات) بشكل آمن.

**المتطلبات:**
-   وصول آمن للموظفين عن بُعد.
-   مصادقة قوية للمستخدمين.
-   تطبيق سياسات أمنية على ترافيك الـ VPN.

### 12.2.2 الحل باستخدام FortiGate

**المكونات الرئيسية:**
-   **FortiGate:** هيكون الـ VPN Server.
-   **SSL VPN:** لسهولة الوصول من أي مكان.
-   **FortiClient:** لإنشاء نفق VPN آمن.
-   **RADIUS/LDAP Authentication:** للمصادقة المركزية للمستخدمين.
-   **Two-Factor Authentication (2FA) with FortiToken:** لزيادة الأمان.
-   **Firewall Policies:** للتحكم في صلاحيات المستخدمين بعد الاتصال بالـ VPN.
-   **Security Profiles:** لتأمين ترافيك الـ VPN.

**خطوات التكوين (ملخص):**

1.  **تكوين SSL VPN:**
    -   فعل SSL VPN على واجهة FortiGate (غالباً `wan1`).
    -   حدد الـ IP Pool اللي هياخد منه مستخدمي الـ VPN IPs.
    -   كون SSL VPN Portal (Web Mode و Tunnel Mode).

2.  **تكوين المصادقة:**
    -   ربط FortiGate بخادم RADIUS أو LDAP (زي Active Directory) للمصادقة المركزية.
    -   فعل المصادقة الثنائية (2FA) باستخدام FortiToken للمستخدمين.
    -   كون User Group لمستخدمي الـ VPN.

3.  **تكوين Firewall Policies:**
    -   **Source Interface:** `ssl.root`.
    -   **Destination Interface:** `lan` (أو الواجهة اللي عليها الموارد الداخلية).
    -   **Source:** User Group لمستخدمي الـ VPN.
    -   **Destination:** الموارد الداخلية اللي عايز تسمح بالوصول ليها.
    -   **Action:** `ACCEPT`.
    -   **NAT:** `Enable`.
    -   **Security Profiles:** فعل الـ Antivirus, IPS, Web Filter, Application Control على ترافيك الـ VPN.

**مقارنة بـ Cisco:**

نفس السيناريو ممكن يتم باستخدام Cisco ASA مع AnyConnect VPN Client. هتستخدم RADIUS/LDAP للمصادقة، وممكن تدمجها مع Cisco Duo لـ 2FA. هتطبق Access-lists أو Firepower Policies للتحكم في الوصول. المفهوم هو نفسه، لكن الأدوات والتكوين بيختلف.

## 12.3 تأمين تطبيقات الويب (Web Application Security)

### 12.3.1 السيناريو

عندك تطبيقات ويب (Web Applications) مستضافة على سيرفرات داخلية، وعايز تحميها من الهجمات الشائعة على الويب (زي SQL Injection, Cross-Site Scripting - XSS) وتوفر وصول آمن للمستخدمين من الإنترنت.

**المتطلبات:**
-   حماية تطبيقات الويب من الهجمات.
-   نشر تطبيقات الويب بأمان على الإنترنت.
-   Load Balancing (اختياري) لو عندك أكتر من سيرفر.

### 12.3.2 الحل باستخدام FortiGate (مع FortiWeb)

FortiGate بيوفر حماية أساسية لتطبيقات الويب من خلال الـ Security Profiles، لكن للحماية المتقدمة، يفضل استخدام FortiWeb (Web Application Firewall - WAF).

**المكونات الرئيسية:**
-   **FortiGate:** هيكون الـ Edge Firewall.
-   **FortiWeb (WAF):** للحماية المتقدمة لتطبيقات الويب.
-   **Virtual IPs (VIPs):** لنشر تطبيقات الويب على الإنترنت.
-   **Firewall Policies:** للسماح بالترافيك لـ FortiWeb.
-   **Security Profiles:** (على FortiGate) للحماية الأساسية.

**خطوات التكوين (ملخص):**

1.  **تكوين Virtual IP (VIP) على FortiGate:**
    -   انتقل إلى **(Policy & Objects) -> (Virtual IPs)**.
    -   انقر على **(Create New)**.
    -   **External IP Address:** الـ Public IP اللي المستخدمين هيوصلوا بيه.
    -   **Mapped IP Address:** الـ IP بتاع سيرفر الويب الداخلي (أو FortiWeb لو هتستخدمه).
    -   **Port Forwarding:** فعلها لو عايز تعمل Port Forwarding لبورت معين (مثلاً: 80, 443).

2.  **تكوين Firewall Policy للسماح بالترافيك:**
    -   **Incoming Interface:** `wan1`.
    -   **Destination Interface:** `lan`.
    -   **Source:** `all`.
    -   **Destination:** الـ VIP اللي عملته.
    -   **Service:** `HTTP`, `HTTPS`.
    -   **Action:** `ACCEPT`.
    -   **Security Profiles:** فعل الـ Antivirus, IPS, Web Filter, Application Control.

3.  **تكوين FortiWeb (لو هتستخدمه):**
    -   هتوصل FortiWeb بين FortiGate وسيرفر الويب.
    -   هتكون الـ Policies على FortiWeb عشان تحمي من هجمات الـ OWASP Top 10 وغيرها.

**مقارنة بـ Cisco:**

Cisco عندها حلول WAF زي Cisco Secure Application (جزء من Firepower) أو عن طريق التكامل مع حلول WAF خارجية. المفهوم هو نفسه: حماية تطبيقات الويب من الهجمات المتخصصة.

## الخلاصة

في الفصل ده، شفنا إزاي كل اللي اتعلمناه في الفصول اللي فاتت بيشتغل مع بعضه في سيناريوهات حقيقية. اتعلمنا إزاي نأمن فروع الشركات، وإزاي نوفر وصول آمن للموظفين عن بُعد، وإزاي نحمي تطبيقات الويب.

الفصل ده بيوريك قوة FortiGate كحل أمني متكامل، وإزاي بيقدر يحل مشاكل أمنية معقدة في بيئات مختلفة. دلوقتي أنت بقيت جاهز إنك تصمم وتنفذ حلول أمنية باستخدام FortiGate في أي بيئة شبكة.

يلا بينا على الفصل الأخير، اللي هنتكلم فيه عن إزاي تستعد لامتحان شهادة FortiGate، وإيه هي أهم النصائح عشان تنجح فيه!

---

*تأليف: Manus AI*
*تاريخ الإنشاء: ديسمبر 2024*

