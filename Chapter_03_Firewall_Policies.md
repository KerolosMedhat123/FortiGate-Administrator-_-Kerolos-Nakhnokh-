
# الفصل الثالث: سياسات جدار الحماية (Firewall Policies)

## نظرة عامة على الفصل

يا جماعة، لو FortiGate ده هو قلب الشبكة، يبقى سياسات جدار الحماية (Firewall Policies) هي الشرايين اللي بتتحكم في كل حركة مرور البيانات جوه القلب ده. الفصل ده هو الأهم في المنهج كله، لأنه بيشرح إزاي FortiGate بياخد قرارات السماح أو الرفض لحركة المرور، وإزاي بنقدر نتحكم في ده.

هنتكلم بالتفصيل عن أنواع السياسات المختلفة، وإزاي بنكونها خطوة بخطوة. هنفهم إيه هي مكونات السياسة الواحدة (المصدر، الوجهة، الخدمة، الإجراء)، وإزاي ترتيب السياسات بيأثر على الأمان والأداء. كمان، هنتعمق في مفهوم الـ NAT (Network Address Translation) اللي بيخلي شبكتك الداخلية تتكلم مع الإنترنت، وهنشوف إزاي FortiGate بيطبق الـ Security Profiles (زي Antivirus و IPS) على السياسات دي.

الفصل ده هو اللي هيخليك تتحكم في FortiGate بجد، وتأمن شبكتك صح. ركز معايا في كل كلمة، عشان ده مفتاح فهم FortiGate كله!

## 3.1 مقدمة إلى سياسات جدار الحماية

### 3.1.1 ما هي سياسة جدار الحماية؟

سياسة جدار الحماية هي مجموعة من القواعد اللي بتحدد إذا كانت حركة مرور معينة مسموح بيها تعدي من FortiGate ولا لأ. كل سياسة بتتكون من مجموعة من المعايير، لو حركة المرور طابقت المعايير دي، بيتم تطبيق الإجراء المحدد في السياسة.

**المبدأ الأساسي:**

FortiGate بيراجع السياسات بالترتيب، من فوق لتحت. أول سياسة تطابق حركة المرور، بيتم تطبيقها، وباقي السياسات مش بتتبص عليها. عشان كده، ترتيب السياسات مهم جداً.

### 3.1.2 المكونات الأساسية للسياسة

كل سياسة جدار حماية بتتكون من المكونات دي:

-   **Incoming Interface (الواجهة الواردة):** الواجهة اللي حركة المرور جاية منها (مثلاً: `lan`).
-   **Outgoing Interface (الواجهة الصادرة):** الواجهة اللي حركة المرور رايحة ليها (مثلاً: `wan1`).
-   **Source (المصدر):** مين اللي بيبعت حركة المرور (IP Address، شبكة، مجموعة مستخدمين).
-   **Destination (الوجهة):** مين اللي بيستقبل حركة المرور (IP Address، شبكة، Virtual IP).
-   **Service (الخدمة):** نوع حركة المرور (HTTP, HTTPS, DNS, Custom Port).
-   **Action (الإجراء):** إيه اللي هيحصل لحركة المرور دي (قبول `Accept`، رفض `Deny`، أو رفض مع إرسال رسالة `Deny (with reset)`).
-   **Security Profiles (ملفات تعريف الأمان):** ميزات أمنية إضافية زي Antivirus, IPS, Web Filter, Application Control.
-   **NAT (Network Address Translation):** تفعيل أو تعطيل الـ NAT.

### 3.1.3 مبدأ الرفض الضمني (Implicit Deny)

ده مبدأ أمني مهم جداً: لو حركة المرور لم تطابق أي سياسة من السياسات اللي أنت مكونها، FortiGate بيرفضها تلقائياً. دي سياسة افتراضية موجودة في آخر قائمة السياسات ومش بتظهرلك، لكنها شغالة دايماً عشان تضمن إن أي حاجة مش مسموح بيها صراحة، هتترفض.

**مقارنة بـ Cisco:**

نفس المبدأ موجود في Cisco ASA و Router Access-lists. دايماً بيكون فيه `deny any any` ضمني في آخر الـ Access-list. ده بيأكد على أهمية إنك تسمح بس باللي أنت عايزه، وترفض أي حاجة تانية.

## 3.2 أنواع السياسات

FortiGate بيسمحلك تكون أنواع مختلفة من السياسات بناءً على حركة المرور:

-   **IPv4 Policy:** للتحكم في حركة مرور IPv4 (الأكثر شيوعاً).
-   **IPv6 Policy:** للتحكم في حركة مرور IPv6.
-   **Multicast Policy:** للتحكم في حركة مرور الـ Multicast.
-   **Local In Policy:** للتحكم في حركة المرور الموجهة لـ FortiGate نفسه (زي الوصول الإداري).

## 3.3 تكوين سياسة جدار حماية (مثال: السماح بالوصول للإنترنت)

هنعمل سياسة بسيطة تسمح للمستخدمين في الشبكة الداخلية (LAN) بالوصول للإنترنت.

**عبر GUI:**
1.  انتقل إلى **(Policy & Objects) -> (Firewall Policy) -> (IPv4 Policy)**.
2.  انقر على **(Create New)**.
3.  **Name:** `Allow_LAN_to_Internet` (اسم وصفي للسياسة).
4.  **Incoming Interface:** `lan` (الواجهة اللي جاي منها الترافيك).
5.  **Outgoing Interface:** `wan1` (الواجهة اللي رايح ليها الترافيك).
6.  **Source:** `all` (أو مجموعة عناوين IP محددة من الشبكة الداخلية).
7.  **Destination:** `all` (أو مجموعة عناوين IP محددة على الإنترنت).
8.  **Service:** `HTTP`, `HTTPS`, `DNS` (أو `all` لو عايز تسمح بكل الخدمات).
9.  **Action:** `ACCEPT`.
10. **NAT:** تأكد إن `NAT` مفعل (عشان الأجهزة الداخلية تطلع بـ IP الـ WAN).
11. **Security Profiles:** ممكن تفعل Antivirus, IPS, Web Filter, Application Control هنا (هنتكلم عنهم بالتفصيل في فصل Security Profiles).
12. **Logging Options:** `Log Allowed Traffic` (عشان تسجل حركة المرور المسموح بيها).
13. انقر على **(OK)**.

**عبر CLI:**
```
config firewall policy
    edit 0 # أو رقم سياسة جديد
        set name "Allow_LAN_to_Internet"
        set srcintf "lan"
        set dstintf "wan1"
        set srcaddr "all"
        set dstaddr "all"
        set service "HTTP" "HTTPS" "DNS"
        set action accept
        set nat enable
        set logtraffic all
    next
end
```

## 3.4 ترتيب السياسات (Policy Order and Optimization)

زي ما قلنا، FortiGate بيراجع السياسات بالترتيب من فوق لتحت. ده بيخلي ترتيب السياسات عامل حاسم في الأمان والأداء.

### 3.4.1 قواعد ترتيب السياسات

-   **السياسات الأكثر تحديداً أولاً (Most Specific First):** السياسات اللي بتحدد حركة مرور معينة جداً (مثلاً: IP واحد ومنفذ واحد) لازم تكون قبل السياسات العامة (مثلاً: أي IP وأي منفذ).
-   **سياسات الرفض قبل سياسات السماح (Deny Before Allow):** لو عايز تمنع حركة مرور معينة، لازم تحط سياسة الرفض بتاعتها قبل أي سياسة سماح ممكن تطابق نفس حركة المرور دي.
-   **السياسات الأكثر استخداماً أولاً (Most Used First):** السياسات اللي بيتم استخدامها كتير، يفضل تكون في الأول عشان FortiGate ميضطرش يراجع سياسات كتير قبل ما يلاقي تطابق.

### 3.4.2 مثال على ترتيب السياسات

تخيل إنك عايز تمنع مستخدم معين (IP: 192.168.1.50) من الوصول للفيسبوك، لكن تسمح لباقي الشبكة بالوصول للإنترنت عادي.

**الترتيب الصحيح:**
1.  **Deny_User_Facebook:** (Source: 192.168.1.50, Destination: Facebook, Service: HTTPS, Action: DENY)
2.  **Allow_LAN_to_Internet:** (Source: LAN, Destination: all, Service: all, Action: ACCEPT)

لو عكست الترتيب، سياسة `Allow_LAN_to_Internet` هتطابق حركة مرور الفيسبوك للمستخدم ده، وهتسمح بيها قبل ما سياسة الرفض تيجي، وبالتالي مش هتشتغل.

**مقارنة بـ Cisco:**

نفس المفهوم في Cisco Access-lists. الأوامر بتتم معالجتها بالترتيب، وأول تطابق بيتم تطبيقه. عشان كده، لازم تحط الـ `deny` الأكثر تحديداً قبل الـ `permit` الأكثر عمومية.

## 3.5 ترجمة عنوان الشبكة (Network Address Translation - NAT)

### 3.5.1 مفهوم NAT

NAT هي عملية تغيير عناوين IP المصدر أو الوجهة لحركة المرور أثناء مرورها عبر FortiGate. بنستخدم الـ NAT عشان:

-   **Source NAT (SNAT):** لما الأجهزة الداخلية (اللي ليها Private IPs) بتحاول توصل للإنترنت (اللي بيحتاج Public IPs). FortiGate بيغير الـ Private IP بتاع الجهاز الداخلي لـ Public IP بتاع FortiGate (غالباً الـ IP بتاع واجهة الـ WAN).
-   **Destination NAT (DNAT) / Virtual IP (VIP):** لما حد من الإنترنت بيحاول يوصل لسيرفر موجود جوه الشبكة الداخلية (اللي ليه Private IP). FortiGate بيغير الـ Public IP اللي الناس بتحاول توصله لـ Private IP بتاع السيرفر الداخلي.

### 3.5.2 تكوين Source NAT (SNAT)

SNAT بيتم تفعيله في سياسة جدار الحماية نفسها.

**عبر GUI:**
1.  في سياسة جدار الحماية (زي `Allow_LAN_to_Internet` اللي عملناها قبل كده).
2.  في قسم **(NAT)**، فعل خيار **(Use Outgoing Interface Address)**. ده بيخلي FortiGate يستخدم الـ IP بتاع واجهة الـ Outgoing (اللي هي `wan1` في المثال بتاعنا) كـ Source IP لحركة المرور اللي طالعة للإنترنت.

**عبر CLI:**
```
config firewall policy
    edit <policy_id>
        set nat enable
    next
end
```

### 3.5.3 تكوين Destination NAT (DNAT) / Virtual IP (VIP)

VIP بيستخدم عشان يسمح بالوصول لسيرفرات داخلية من الإنترنت.

**الخطوة 1: إنشاء Virtual IP (GUI):**
1.  انتقل إلى **(Policy & Objects) -> (Virtual IPs)**.
2.  انقر على **(Create New) -> (Virtual IP)**.
3.  **Name:** `Web_Server_VIP`.
4.  **External IP Address/Range:** الـ Public IP اللي الناس هتوصل بيه من الإنترنت (مثلاً: `203.0.113.10`).
5.  **Mapped IP Address/Range:** الـ Private IP بتاع السيرفر الداخلي (مثلاً: `192.168.1.200`).
6.  **Port Forwarding:** لو عايز توجه بورت معين (مثلاً: `HTTP` أو `HTTPS`).

**عبر CLI:**
```
config firewall vip
    edit "Web_Server_VIP"
        set extip 203.0.113.10
        set mappedip "192.168.1.200"
        set extintf "wan1"
        set portforward enable
        set protocol tcp
        set mappedport 80
        set extport 80
    next
end
```

**الخطوة 2: استخدام Virtual IP في سياسة جدار الحماية (GUI):**
1.  انتقل إلى **(Policy & Objects) -> (Firewall Policy) -> (IPv4 Policy)**.
2.  انقر على **(Create New)**.
3.  **Incoming Interface:** `wan1`.
4.  **Outgoing Interface:** `lan` (أو الواجهة اللي عليها السيرفر).
5.  **Source:** `all`.
6.  **Destination:** `Web_Server_VIP` (الـ VIP اللي لسه عاملينه).
7.  **Service:** `HTTP`, `HTTPS`.
8.  **Action:** `ACCEPT`.
9.  **NAT:** تأكد إن `NAT` غير مفعل (لأن الـ VIP هو اللي بيعمل الـ NAT).

**مقارنة بـ Cisco:**

Cisco ASA بيستخدم Static NAT أو Port Address Translation (PAT) لنفس الغرض. المفهوم هو نفسه: ترجمة عناوين IP للسماح بالاتصال بين الشبكات المختلفة. FortiGate بيسهل الموضوع ده عن طريق الـ VIPs.

## 3.6 ملفات تعريف الأمان (Security Profiles)

### 3.6.1 مفهوم Security Profiles

Security Profiles هي مجموعة من الميزات الأمنية المتقدمة اللي FortiGate بيطبقها على حركة المرور اللي بتعدي من خلاله. دي اللي بتخلي FortiGate جدار حماية من الجيل التالي (NGFW) أو UTM. بتشمل:

-   **Antivirus:** بيكتشف ويمنع الفيروسات والبرامج الضارة.
-   **Intrusion Prevention System (IPS):** بيكتشف ويمنع محاولات الاختراق والهجمات.
-   **Web Filter:** بيتحكم في تصفح الويب ويمنع الوصول للمواقع الضارة أو غير المرغوب فيها.
-   **Application Control:** بيتعرف على التطبيقات (حتى لو بتستخدم بورتات غير قياسية) ويتحكم فيها.
-   **Data Leakage Prevention (DLP):** بيمنع تسرب البيانات الحساسة خارج الشبكة.
-   **SSL/SSH Inspection:** بيفحص حركة المرور المشفرة (HTTPS, SSH) عشان يكتشف التهديدات المخفية جواها.

### 3.6.2 ربط Security Profiles بسياسة جدار الحماية

عشان FortiGate يطبق الـ Security Profiles دي، لازم تفعلها في سياسة جدار الحماية.

**عبر GUI:**
1.  في سياسة جدار الحماية (زي `Allow_LAN_to_Internet`).
2.  في قسم **(Security Profiles)**، فعل الميزات اللي عايزها (Antivirus, IPS, Web Filter, Application Control, SSL/SSH Inspection).
3.  لكل ميزة، اختار الـ Profile المناسب (ممكن تستخدم الـ Profile الافتراضي أو تعمل Profile مخصص).

**عبر CLI:**
```
config firewall policy
    edit <policy_id>
        set av-profile "default"
        set ips-sensor "default"
        set webfilter-profile "default"
        set application-list "default"
        set ssl-ssh-profile "deep-inspection"
    next
end
```

**مقارنة بـ Cisco:**

في Cisco Firepower NGFW، بتستخدم Access Control Policies عشان تربط ميزات الأمان المتقدمة (زي AMP, IPS, URL Filtering, Application Control) بحركة المرور. المفهوم هو نفسه: تطبيق حماية متعددة الطبقات على الترافيك.

## الخلاصة

الفصل ده كان هو قلب المنهج! دلوقتي أنت فهمت إزاي سياسات جدار الحماية بتشتغل على FortiGate، وإزاي تتحكم في حركة المرور بشكل كامل. عرفت إيه هي مكونات السياسة، وإزاي ترتيبها مهم جداً، وإزاي الـ NAT بيخلي شبكتك تتكلم مع العالم الخارجي.

الأهم من ده كله، إنك فهمت إزاي تربط الـ Security Profiles بالسياسات عشان FortiGate يقدر يوفر حماية متكاملة ضد أحدث التهديدات. لو قدرت تستوعب الفصل ده كويس وتطبقه عملي، فأنت قطعت شوط كبير جداً في طريقك لتكون خبير FortiGate.

يلا بينا على الفصل اللي جاي عشان نكمل رحلتنا في عالم FortiGate المثير!

---

*تأليف: Manus AI*
*تاريخ الإنشاء: ديسمبر 2024*

