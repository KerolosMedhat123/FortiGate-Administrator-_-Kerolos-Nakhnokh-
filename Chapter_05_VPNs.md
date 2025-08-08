# الفصل الخامس: الشبكات الافتراضية الخاصة (VPNs - Virtual Private Networks)

## نظرة عامة على الفصل

يا شباب، بعد ما فهمنا إزاي FortiGate بيتحكم في حركة المرور وبيعمل مصادقة للمستخدمين، دلوقتي جه وقت نتكلم عن الـ VPNs (Virtual Private Networks). الـ VPNs دي حاجة أساسية جداً في أي شبكة حديثة، خصوصاً مع انتشار العمل عن بُعد والفروع المتعددة للشركات.

في الفصل ده، هنتعمق في أنواع الـ VPNs اللي FortiGate بيدعمها: الـ SSL VPN اللي بيسمح للمستخدمين يوصلوا للشبكة من أي مكان بسهولة، والـ IPsec VPN اللي بيستخدم لربط الفروع ببعضها بشكل آمن. هنشرح كل نوع بالتفصيل، وإزاي تكونه خطوة بخطوة، وإيه هي أفضل الممارسات لكل نوع.

كمان، هنتعلم إزاي نستكشف الأخطاء ونحل المشاكل اللي ممكن تحصل في الـ VPNs. الفصل ده هيخليك تقدر توفر اتصال آمن وموثوق بيه لشبكتك، سواء للموظفين اللي بيشتغلوا من بره، أو لربط فروع الشركة ببعضها. يلا بينا نخش في التفاصيل!

## 5.1 مقدمة إلى VPNs

### 5.1.1 ما هي VPN؟

الـ VPN هي شبكة خاصة افتراضية بتسمحلك تعمل اتصال آمن ومشفّر عبر شبكة عامة (زي الإنترنت). تخيل إنك بتبني نفق سري ومحمي جوه الإنترنت، أي داتا بتعدي جوه النفق ده بتكون مشفرة ومحمية من أي حد بيحاول يتجسس عليها.

**ليه بنستخدم VPN؟**
-   **الأمان (Security):** تشفير البيانات وحمايتها من التنصت.
-   **الخصوصية (Privacy):** إخفاء عنوان IP الحقيقي بتاعك.
-   **الوصول عن بُعد (Remote Access):** السماح للموظفين بالوصول لموارد الشركة من أي مكان.
-   **ربط الفروع (Site-to-Site Connectivity):** ربط فروع الشركة ببعضها بشكل آمن.

### 5.1.2 أنواع VPNs في FortiGate

FortiGate بيدعم نوعين رئيسيين من الـ VPNs:

-   **SSL VPN:**
    -   بيستخدم بروتوكول SSL/TLS (نفس البروتوكول اللي بيستخدم في HTTPS).
    -   سهل الاستخدام للمستخدمين النهائيين، ممكن يوصلوا من أي متصفح ويب أو باستخدام FortiClient.
    -   مناسب للوصول عن بُعد (Remote Access) للمستخدمين الفرديين.
    -   بيشتغل على بورت 443 (أو أي بورت تاني تحدده)، وده بيخليه يعدي من معظم جدران الحماية بسهولة.

-   **IPsec VPN:**
    -   بيستخدم بروتوكول IPsec (Internet Protocol Security).
    -   أكثر تعقيداً في التكوين من SSL VPN، لكنه بيوفر أمان أعلى وأداء أفضل في بعض السيناريوهات.
    -   مناسب لربط الفروع ببعضها (Site-to-Site) أو للوصول عن بُعد (Remote Access) باستخدام FortiClient.

**مقارنة بـ Cisco:**

Cisco أيضاً بتدعم SSL VPN (عن طريق AnyConnect Client) و IPsec VPN (Site-to-Site و Remote Access) على أجهزة ASA و Routers. المفهوم واحد، لكن طريقة التكوين بتختلف.

## 5.2 SSL VPN (الوصول عن بُعد - Remote Access)

### 5.2.1 مفهوم SSL VPN

SSL VPN بيسمح للمستخدمين بالوصول الآمن للشبكة الداخلية من أي مكان باستخدام متصفح ويب (Web Mode) أو برنامج FortiClient (Tunnel Mode). الـ Web Mode بيسمح بالوصول لتطبيقات الويب فقط، أما الـ Tunnel Mode فبيعمل نفق كامل وبيخلي جهاز المستخدم كأنه جزء من الشبكة الداخلية.

### 5.2.2 تكوين SSL VPN

**الخطوة 1: تفعيل SSL VPN على واجهة (GUI):**
1.  انتقل إلى **(VPN) -> (SSL-VPN Settings)**.
2.  **Listen on Interface(s):** اختار الواجهة اللي FortiGate هيستقبل عليها اتصالات الـ VPN (غالباً `wan1`).
3.  **Listen Port:** البورت اللي هيشتغل عليه الـ SSL VPN (الافتراضي 443، ممكن تغيره لـ 10443 مثلاً).
4.  **Server Certificate:** اختار الشهادة اللي هيستخدمها FortiGate (ممكن تستخدم شهادة Fortinet الافتراضية أو شهادة خاصة بيك).

**عبر CLI:**
```
config vpn ssl web portal
    edit "full-access"
        set tunnel-mode enable
        set web-mode enable
        set ip-pools "SSLVPN_TUNNEL_ADDR1"
        set split-tunneling enable
    next
end

config vpn ssl settings
    set sslvpn-enable enable
    set servercert "Fortinet_Factory"
    set tunnel-ip-pools "SSLVPN_TUNNEL_ADDR1"
    set source-interface "wan1"
    set source-address "all"
    set default-portal "full-access"
    set listen-port 10443
end
```

**الخطوة 2: تكوين مجموعة مستخدمين SSL VPN (GUI):**
1.  انتقل إلى **(User & Authentication) -> (User Groups)**.
2.  انقر على **(Create New)**.
3.  **Name:** `SSLVPN_Users`.
4.  **Type:** `Firewall`.
5.  **Members:** ضيف المستخدمين اللي هيستخدموا الـ SSL VPN (ممكن يكونوا Local Users أو من RADIUS/LDAP).

**الخطوة 3: تكوين SSL VPN Portal (GUI):**
1.  انتقل إلى **(VPN) -> (SSL-VPN Portals)**.
2.  انقر على **(Create New)** أو عدّل الـ `full-access` Portal.
3.  **Tunnel Mode:** فعلها لو عايز المستخدمين يستخدموا FortiClient.
4.  **Web Mode:** فعلها لو عايز المستخدمين يوصلوا من المتصفح.
5.  حدد الـ IP Pool اللي هياخد منه المستخدمين IPs لما يتصلوا بالـ VPN.
6.  حدد الموارد اللي هيقدروا يوصلولها (Web Bookmarks, File Shares).

**الخطوة 4: إنشاء سياسة جدار حماية لـ SSL VPN (GUI):**
1.  انتقل إلى **(Policy & Objects) -> (Firewall Policy) -> (IPv4 Policy)**.
2.  انقر على **(Create New)**.
3.  **Name:** `SSLVPN_to_LAN`.
4.  **Incoming Interface:** `ssl.root` (دي الواجهة الافتراضية لـ SSL VPN).
5.  **Outgoing Interface:** `lan` (الواجهة اللي عليها الموارد اللي عايز توصلها).
6.  **Source:** `SSLVPN_Users` (مجموعة المستخدمين اللي عملتها).
7.  **Destination:** الشبكة الداخلية اللي عايز توصلها (مثلاً: `LAN_Subnet`).
8.  **Service:** `all` (أو الخدمات المحددة اللي عايز تسمح بيها).
9.  **Action:** `ACCEPT`.
10. **NAT:** تأكد إن `NAT` مفعل.

**مقارنة بـ Cisco AnyConnect:**

Cisco AnyConnect VPN Client بيوفر وظائف مشابهة لـ FortiClient في الـ SSL VPN. بيسمح بالوصول عن بُعد، وبيقدر يعمل Tunnel Mode و Web Mode. التكوين بيتم على Cisco ASA أو Firepower.

## 5.3 IPsec VPN (من موقع لموقع - Site-to-Site)

### 5.3.1 مفهوم IPsec VPN Site-to-Site

IPsec VPN Site-to-Site بيستخدم لربط شبكتين منفصلتين (زي المقر الرئيسي وفرع الشركة) عبر الإنترنت بشكل آمن. بيعمل نفق مشفر بين جهازي FortiGate (أو FortiGate وجهاز VPN تاني زي Cisco Router/ASA)، وبيخلي الشبكتين دول كأنهم شبكة واحدة.

### 5.3.2 تكوين IPsec VPN Site-to-Site

**الخطوة 1: تكوين Phase 1 (IKE Gateway) (GUI):**
1.  انتقل إلى **(VPN) -> (IPsec Tunnels)**.
2.  انقر على **(Create New) -> (IPsec Tunnel)**.
3.  **Name:** `Main_Office_to_Branch`.
4.  **Template Type:** `Site to Site`.
5.  **Remote Gateway:** `Static IP Address`.
6.  **IP Address:** عنوان IP الـ Public بتاع الفرع التاني.
7.  **Interface:** الواجهة اللي FortiGate هيستخدمها للاتصال (غالباً `wan1`).
8.  **Pre-shared Key:** كلمة سر مشتركة بين الجهازين (مهم جداً تكون قوية).
9.  **Phase 1 Proposal:**
    -   **Encryption:** `AES256`.
    -   **Authentication:** `SHA256`.
    -   **Diffie-Hellman Group:** `5`.
    -   **Keylife:** `86400` ثانية.

**عبر CLI:**
```
config vpn ipsec phase1-interface
    edit "Main_Office_to_Branch_P1"
        set interface "wan1"
        set ike-version 1
        set local-gw 1.1.1.1 # Public IP بتاع الجهاز ده
        set remote-gw 2.2.2.2 # Public IP بتاع الجهاز التاني
        set psksecret "MyStrongPskSecret!"
        set proposal aes256-sha256
        set dhgrp 5
        set keylife 86400
    next
end
```

**الخطوة 2: تكوين Phase 2 (IPsec Tunnel) (GUI):**
1.  بعد ما تخلص Phase 1، هتلاقي خيار **(Create Phase 2)**.
2.  **Name:** `Main_Office_to_Branch_P2`.
3.  **Local Address:** الشبكة الداخلية للمقر الرئيسي (مثلاً: `192.168.1.0/24`).
4.  **Remote Address:** الشبكة الداخلية للفرع (مثلاً: `192.168.2.0/24`).
5.  **Phase 2 Proposal:**
    -   **Encryption:** `AES256`.
    -   **Authentication:** `SHA256`.
    -   **Diffie-Hellman Group:** `5` (لازم تكون نفس اللي في Phase 1).
    -   **Keylife:** `3600` ثانية.

**عبر CLI:**
```
config vpn ipsec phase2-interface
    edit "Main_Office_to_Branch_P2"
        set phase1name "Main_Office_to_Branch_P1"
        set proposal aes256-sha256
        set dhgrp 5
        set keylife 3600
        set src-subnet 192.168.1.0 255.255.255.0
        set dst-subnet 192.168.2.0 255.255.255.0
    next
end
```

**الخطوة 3: إنشاء Static Route (GUI):**
1.  انتقل إلى **(Network) -> (Static Routes)**.
2.  انقر على **(Create New)**.
3.  **Destination:** الشبكة البعيدة (مثلاً: `192.168.2.0/24`).
4.  **Device:** اختر الـ IPsec Tunnel اللي عملته (مثلاً: `Main_Office_to_Branch`).

**عبر CLI:**
```
config router static
    edit 0
        set dst 192.168.2.0 255.255.255.0
        set device "Main_Office_to_Branch_P1"
    next
end
```

**الخطوة 4: إنشاء سياسة جدار حماية لـ IPsec VPN (GUI):**

هتعمل سياستين، واحدة تسمح بالترافيك من المقر الرئيسي للفرع، وواحدة العكس.

**السياسة الأولى (المقر الرئيسي للفرع):**
1.  **Incoming Interface:** `lan`.
2.  **Outgoing Interface:** `Main_Office_to_Branch` (اسم الـ IPsec Tunnel).
3.  **Source:** الشبكة الداخلية للمقر الرئيسي.
4.  **Destination:** الشبكة الداخلية للفرع.
5.  **Service:** `all`.
6.  **Action:** `ACCEPT`.
7.  **NAT:** `Disable` (لأن الـ IPsec بيحافظ على الـ IPs الأصلية).

**السياسة الثانية (الفرع للمقر الرئيسي):**
1.  **Incoming Interface:** `Main_Office_to_Branch`.
2.  **Outgoing Interface:** `lan`.
3.  **Source:** الشبكة الداخلية للفرع.
4.  **Destination:** الشبكة الداخلية للمقر الرئيسي.
5.  **Service:** `all`.
6.  **Action:** `ACCEPT`.
7.  **NAT:** `Disable`.

**مقارنة بـ Cisco IPsec VPN:**

تكوين IPsec VPN في Cisco (على Routers أو ASA) بيشمل نفس المفاهيم: Phase 1 (ISAKMP/IKE) و Phase 2 (IPsec). بتحدد الـ Encryption, Hashing, DH Group, Keylife. وبتستخدم Crypto Maps أو VTI لربط الـ VPN بالواجهات. FortiGate بيقدم واجهة أبسط شوية، لكن المبادئ الأساسية هي هي.

## 5.4 استكشاف أخطاء VPN وإصلاحها (VPN Troubleshooting)

مشاكل الـ VPN ممكن تكون محبطة، لكن FortiGate بيوفر أدوات قوية للتشخيص.

### 5.4.1 مشاكل SSL VPN الشائعة

-   **عدم القدرة على الاتصال:**
    -   تأكد من أن FortiGate يستمع على البورت الصحيح (مثلاً 10443) وأن البورت ده مفتوح على جدار الحماية الخارجي (لو فيه).
    -   تأكد من أن المستخدم موجود في مجموعة SSL VPN الصحيحة.
    -   تأكد من أن FortiClient مثبت بشكل صحيح.
-   **عدم القدرة على الوصول للموارد بعد الاتصال:**
    -   تأكد من أن الـ IP Pool بتاع الـ SSL VPN مش متعارض مع أي شبكة تانية.
    -   تأكد من وجود Static Route للشبكة الداخلية عبر الـ SSL VPN Interface (`ssl.root`).
    -   تأكد من وجود Firewall Policy تسمح بالترافيك من `ssl.root` للشبكة الداخلية.

### 5.4.2 مشاكل IPsec VPN الشائعة

-   **Phase 1 Down:**
    -   **Pre-shared Key:** تأكد إن الـ Pre-shared Key هو هو على الجهازين.
    -   **IP Addresses:** تأكد إن الـ Public IPs صحيحة على الجهازين.
    -   **Phase 1 Proposals:** تأكد إن الـ Encryption, Authentication, DH Group, Keylife متطابقين على الجهازين.
    -   **Firewall Policy:** تأكد إن فيه سياسة تسمح بمرور ترافيك IKE (UDP 500, UDP 4500) بين الجهازين.
-   **Phase 2 Down:**
    -   **Phase 2 Proposals:** تأكد إن الـ Encryption, Authentication, DH Group, Keylife متطابقين.
    -   **Local/Remote Subnets:** تأكد إن الشبكات المحلية والبعيدة معرفة بشكل صحيح على الجهازين.
    -   **Static Routes:** تأكد من وجود Static Route للشبكة البعيدة عبر الـ IPsec Tunnel.
    -   **Firewall Policy:** تأكد من وجود سياسات تسمح بالترافيك بين الشبكتين عبر الـ IPsec Tunnel.

### 5.4.3 أدوات التشخيص (CLI)

-   **`diagnose vpn tunnel list`:** لعرض حالة أنفاق الـ VPN.
-   **`diagnose vpn ike log level -1`:** لتفعيل تسجيل تفاصيل IKE (Phase 1).
-   **`diagnose debug application ike -1`:** لتفعيل تصحيح أخطاء IKE.
-   **`diagnose debug application sslvpn -1`:** لتفعيل تصحيح أخطاء SSL VPN.
-   **`diagnose sniffer packet`:** لالتقاط الحزم على الواجهات لمعرفة إذا كانت الحزم بتوصل أو بتخرج.
-   **`diagnose debug flow`:** لتتبع مسار الحزم داخل FortiGate.

**مقارنة بـ Cisco VPN Troubleshooting:**

Cisco عندها أوامر `show crypto isakmp sa` و `show crypto ipsec sa` لمراقبة حالة الـ VPNs، وأوامر `debug crypto isakmp` و `debug crypto ipsec` لتتبع مشاكل الـ VPN. المفهوم هو نفسه: استخدام أدوات التشخيص المتاحة لتحديد المشكلة في الـ Phase 1 أو Phase 2.

## الخلاصة

في الفصل ده، غطينا موضوع الـ VPNs المهم جداً على FortiGate. عرفنا الفرق بين SSL VPN و IPsec VPN، وإمتى نستخدم كل واحد. اتعلمنا إزاي نكونهم خطوة بخطوة، وإزاي نربطهم بالمستخدمين وسياسات جدار الحماية.

الأهم من ده كله، إننا عرفنا إزاي نستكشف الأخطاء ونحل المشاكل الشائعة في الـ VPNs باستخدام أدوات التشخيص القوية اللي FortiGate بيوفرها. دلوقتي أنت بقيت جاهز إنك توفر اتصال آمن وموثوق بيه لشبكتك، سواء للموظفين أو لربط الفروع.

يلا بينا على الفصل اللي جاي عشان ندخل في عالم الـ Security Profiles والميزات الأمنية المتقدمة اللي بتخلي FortiGate جدار حماية لا يستهان به!

---

*تأليف: Manus AI*
*تاريخ الإنشاء: ديسمبر 2024*

