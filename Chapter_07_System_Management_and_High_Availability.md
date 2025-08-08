# الفصل السابع: إدارة النظام والتوافرية العالية (System Management and High Availability)

## نظرة عامة على الفصل

يا جماعة، بعد ما اتعلمنا إزاي FortiGate بيحمي الشبكة وبيتحكم في كل حاجة، دلوقتي جه وقت نتكلم عن إزاي نحافظ على الجهاز ده شغال زي الفل، وإزاي نضمن إنه ميفصلش أبداً! الفصل ده هيركز على إدارة النظام (System Management) والتوافرية العالية (High Availability - HA).

هنتكلم عن أهمية تسجيل السجلات (Logging) ومراقبة الأداء (Monitoring) عشان نعرف إيه اللي بيحصل في الشبكة والجهاز. وهنشوف إزاي FortiGate بيقدر يعمل تقارير (Reporting) وتنبيهات (Alerting) عشان نكون دايماً في الصورة. الأهم من ده كله، هنتعمق في مفهوم الـ HA، اللي بيخلي عندك جهازين FortiGate شغالين مع بعض، بحيث لو واحد وقع، التاني يشتغل مكانه على طول من غير ما تحس بأي حاجة. ده بيضمن إن شبكتك تفضل شغالة 24/7.

الفصل ده هيخليك مطمن على شبكتك، وعارف إنها محمية ومستقرة. يلا بينا نخش في التفاصيل!

## 7.1 تسجيل السجلات والمراقبة (Logging and Monitoring)

### 7.1.1 أهمية السجلات (Logs)

السجلات هي عينك على الشبكة. بتسجل كل حاجة بتحصل على FortiGate: مين دخل، مين خرج، إيه المواقع اللي اتفتحت، إيه الهجمات اللي اتصدت، وإيه المشاكل اللي حصلت. من غير السجلات، أنت أعمى ومش هتعرف إيه اللي بيحصل في شبكتك.

**أنواع السجلات الرئيسية:**
-   **Traffic Logs:** بتسجل كل حركة المرور اللي بتعدي من FortiGate (مين بيكلم مين، على بورت إيه، وإيه السياسة اللي سمحت بكده).
-   **Event Logs:** بتسجل الأحداث اللي بتحصل على الجهاز نفسه (زي تسجيل الدخول، تغيير التكوين، مشاكل النظام).
-   **Security Logs:** بتسجل الأحداث الأمنية (زي اكتشاف فيروس، هجوم IPS، حظر موقع).

### 7.1.2 عرض السجلات على FortiGate

**عبر GUI:**
1.  انتقل إلى **(Log & Report) -> (Log Events)**.
2.  هتلاقي أنواع مختلفة من السجلات (Traffic, System, Security). تقدر تختار النوع اللي عايز تشوفه.
3.  ممكن تعمل Filter عشان تدور على حاجة معينة (مثلاً: IP معين، أو تاريخ معين).

**عبر CLI:**
```
# لعرض سجلات الترافيك
execute log display traffic

# لعرض سجلات النظام
execute log display event

# لعرض سجلات الأمن
execute log display utm
```

### 7.1.3 إعدادات تسجيل السجلات (Log Settings)

FortiGate ممكن يسجل السجلات محلياً على الجهاز، أو يبعتها لخادم خارجي زي FortiAnalyzer أو Syslog server.

**عبر GUI:**
1.  انتقل إلى **(Log & Report) -> (Log Settings)**.
2.  **Local Log:** فعلها لو عايز تسجل السجلات على الجهاز نفسه.
3.  **FortiAnalyzer / FortiManager:** فعلها لو عندك FortiAnalyzer أو FortiManager وعايز تبعتله السجلات.
4.  **Syslog:** فعلها لو عايز تبعت السجلات لـ Syslog server خارجي.

**عبر CLI:**
```
config log memory setting
    set status enable
end

config log fortianalyzer setting
    set status enable
    set server "10.0.0.200" # IP FortiAnalyzer
    set upload-option realtime
end

config log syslogd setting
    set status enable
    set server "10.0.0.201" # IP Syslog Server
    set port 514
end
```

**مقارنة بـ Cisco Logging:**

Cisco أيضاً بتدعم تسجيل السجلات محلياً (Flash) أو إرسالها لـ Syslog server خارجي. الأوامر زي `logging host <ip>` و `logging trap <level>`. المفهوم واحد: جمع السجلات لتحليلها ومراقبة الشبكة.

## 7.2 التقارير والتنبيهات (Reporting and Alerting)

### 7.2.1 التقارير (Reporting)

التقارير بتساعدك تحول السجلات الخام لمعلومات مفيدة ومفهومة. FortiGate بيقدر يعمل تقارير عن:
-   أكثر المستخدمين استخداماً للإنترنت.
-   أكثر التطبيقات استخداماً.
-   أكثر التهديدات شيوعاً.
-   حالة الجهاز وأداءه.

**عبر GUI:**
1.  انتقل إلى **(Log & Report) -> (Report Settings)**.
2.  ممكن تعمل تقارير مخصصة أو تستخدم القوالب الجاهزة.
3.  ممكن تحدد مواعيد لإنشاء التقارير وإرسالها بالإيميل.

### 7.2.2 التنبيهات (Alerting)

التنبيهات بتخليك تعرف أول بأول لو حصل أي حدث مهم أو مشكلة. ممكن FortiGate يبعتلك إيميل، أو رسالة SMS، أو SNMP Trap لو حصل حاجة معينة (زي ارتفاع استخدام الـ CPU، أو اكتشاف فيروس خطير).

**عبر GUI:**
1.  انتقل إلى **(System) -> (Alert E-mail)** أو **(SNMP)**.
2.  حدد الشروط اللي عايز FortiGate يبعتلك عليها تنبيه.

**مقارنة بـ Cisco:**

Cisco بتوفر أيضاً إمكانيات تقارير وتنبيهات من خلال منتجات زي Cisco Prime Infrastructure أو Cisco DNA Center، أو عن طريق إرسال SNMP Traps لـ NMS (Network Management System).

## 7.3 مفاهيم التوافرية العالية (High Availability - HA)

### 7.3.1 لماذا نحتاج إلى HA؟

الـ HA هي الحل اللي بيضمن إن شبكتك تفضل شغالة حتى لو حصل عطل في جهاز FortiGate. تخيل إن FortiGate هو البوابة الوحيدة للإنترنت في شركتك، لو وقع، الشركة كلها هتقف. الـ HA بيحل المشكلة دي عن طريق استخدام جهازين FortiGate بيشتغلوا مع بعض كجهاز واحد.

**الهدف من HA:**
-   **الاستمرارية (Business Continuity):** ضمان إن الخدمات الحيوية تفضل شغالة.
-   **تقليل وقت التوقف (Minimize Downtime):** لو جهاز وقع، التاني بيشتغل مكانه في ثواني.
-   **تحسين الأداء (Performance Improvement):** في بعض أنواع الـ HA (Active-Active)، ممكن الجهازين يشتغلوا مع بعض عشان يزودوا الأداء.

### 7.3.2 أنواع HA في FortiGate

FortiGate بيدعم نوعين رئيسيين من الـ HA:

-   **Active-Passive (Recommended for most deployments):**
    -   جهاز واحد بيكون `Active` (شغال وبيعدي الترافيك).
    -   الجهاز التاني بيكون `Passive` (مستعد، ومستني أي لحظة عشان يشتغل مكان الـ Active).
    -   كل الترافيك بيعدي من الـ Active، ولو وقع، الـ Passive بيتحول لـ Active في ثواني.
    -   سهل التكوين والإدارة.

-   **Active-Active:**
    -   الجهازين بيكونوا `Active` وبيعدوا الترافيك في نفس الوقت.
    -   بيوفر أداء أعلى (Load Balancing).
    -   أكثر تعقيداً في التكوين والإدارة.

**مكونات HA:**
-   **Heartbeat Links:** كابلات خاصة بين الجهازين عشان يكلموا بعض ويراقبوا حالة بعض.
-   **Session Synchronization:** الجهاز الـ Active بيبعت معلومات الـ Sessions للـ Passive عشان لو حصل Failover، الـ Sessions تفضل شغالة من غير ما المستخدمين يحسوا بحاجة.
-   **Failover Detection:** الجهازين بيراقبوا بعض، ولو واحد وقع (مبقتش بيبعت Heartbeat)، التاني بيعرف وبيتحول لـ Active.

**مقارنة بـ Cisco HA:**

Cisco عندها حلول HA مشابهة:
-   **HSRP/VRRP/GLBP:** لـ Routers و Layer 3 Switches (مكافئ لـ Active-Passive و Active-Active).
-   **Cisco ASA HA:** بيدعم Active-Passive و Active-Active، وبيستخدم نفس مفاهيم Heartbeat و Session Synchronization.

## 7.4 تكوين HA (Active-Passive)

### 7.4.1 المتطلبات الأساسية (Prerequisites)

-   جهازين FortiGate من نفس الموديل ونفس إصدار الـ Firmware.
-   نفس عدد الواجهات ونفس نوعها.
-   كابلات Heartbeat بين الجهازين (يفضل استخدام منافذ مخصصة للـ HA).
-   توصيل الكابلات الفيزيائية للواجهات (LAN, WAN) من الجهازين لنفس السويتشات.

### 7.4.2 خطوات التكوين (GUI)

**الخطوة 1: الإعدادات الأولية على الجهازين:**
-   تأكد إن الجهازين عليهم نفس إصدار الـ Firmware.
-   اعمل Reset Factory Defaults للجهازين عشان تبدأ من الصفر.
-   وصل الجهازين ببعض بكابلات الـ Heartbeat (يفضل استخدام منفذين منفصلين).

**الخطوة 2: تكوين الجهاز الأول (Primary/Master):**
1.  انتقل إلى **(System) -> (HA)**.
2.  **HA Mode:** `Active-Passive`.
3.  **Device Priority:** `200` (ده هيكون الـ Master، فندي له أولوية أعلى).
4.  **Group Name:** `My_FortiGate_HA` (اسم مجموعة الـ HA).
5.  **Password:** كلمة سر للـ HA (مهمة جداً).
6.  **Heartbeat Interfaces:** اختار الواجهات اللي هتستخدمها للـ Heartbeat (مثلاً: `port5`, `port6`).
7.  **Session Pickup:** `Enable` (عشان الـ Sessions متتقطعش وقت الـ Failover).
8.  **Monitor Interfaces:** اختار الواجهات اللي FortiGate هيراقبها عشان يكتشف لو حصل فيها مشكلة (مثلاً: `wan1`, `lan`). لو أي واجهة من دول وقعت، الجهاز هيعمل Failover.
9.  انقر على **(OK)**.

**عبر CLI (على الجهاز الأول):**
```
config system ha
    set mode a-p
    set group-name "My_FortiGate_HA"
    set password "MyHAPassword!"
    set priority 200
    set hbdev "port5" 50 "port6" 100 # الواجهات والأولويات
    set session-pickup enable
    set monitor "wan1" "lan"
end
```

**الخطوة 3: تكوين الجهاز الثاني (Secondary/Slave):**
1.  نفس الخطوات اللي فاتت، لكن:
    -   **Device Priority:** `100` (أقل من الـ Master).
    -   **Group Name:** `My_FortiGate_HA` (نفس الاسم).
    -   **Password:** نفس كلمة السر.
    -   **Heartbeat Interfaces:** نفس الواجهات.
    -   **Session Pickup:** `Enable`.
    -   **Monitor Interfaces:** نفس الواجهات.
2.  انقر على **(OK)**. الجهاز ده هيعمل Reboot وهينضم لمجموعة الـ HA.

### 7.4.3 التحقق من HA (Verification)

**عبر GUI:**
1.  انتقل إلى **(System) -> (HA)**.
2.  هتشوف حالة الجهازين (Master, Slave) وحالة الـ Heartbeat والـ Session Synchronization.

**عبر CLI:**
```
get system ha status
```

**مثال على مخرجات `get system ha status`:**
```
HA Status: Master
HA Group Member: 1
HA Group ID: 0
HA Group Name: My_FortiGate_HA
Mode: Active-Passive
Master: FG100D-1 (200)
Slave: FG100D-2 (100)
Heartbeat: OK
Session Sync: OK
```

### 7.4.4 عملية الـ Failover

لو الجهاز الـ Active وقع (سواء بسبب عطل في الهاردوير، أو مشكلة في السوفتوير، أو انقطاع في أحد الـ Monitor Interfaces)، الجهاز الـ Passive هيكتشف ده وهيتحول لـ Active. العملية دي بتاخد ثواني قليلة، ومع الـ Session Synchronization، المستخدمين مش هيحسوا بأي انقطاع في معظم الحالات.

**مقارنة بـ Cisco ASA Failover:**

Cisco ASA Failover بيشتغل بنفس المفهوم. جهاز Active وجهاز Standby. بيستخدموا Heartbeat Links لمراقبة بعض، وبيعملوا Session Synchronization. لو الـ Active وقع، الـ Standby بيتحول لـ Active. الأوامر بتختلف، لكن المبادئ هي هي.

## الخلاصة

في الفصل ده، اتعلمنا إزاي ندير نظام FortiGate بشكل فعال، من خلال تسجيل السجلات والمراقبة، وإنشاء التقارير والتنبيهات. الأهم من ده كله، إننا فهمنا قوة التوافرية العالية (HA) وإزاي بتضمن استمرارية عمل شبكتك حتى في حالة الأعطال.

دلوقتي أنت بقيت قادر على بناء شبكة مستقرة ومحمية، وتقدر تتعامل مع أي مشكلة ممكن تحصل بثقة. الفصل ده بيقفل جزء مهم جداً من رحلتنا في FortiGate، وبيخليك جاهز إنك تدخل في مواضيع متقدمة أكتر. يلا بينا على الفصل اللي جاي!

---

*تأليف: Manus AI*
*تاريخ الإنشاء: ديسمبر 2024*

