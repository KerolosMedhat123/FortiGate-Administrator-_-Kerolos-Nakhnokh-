# الفصل العاشر: استكشاف الأخطاء وإصلاحها وأدوات التشخيص (Troubleshooting and Diagnostic Tools)

## نظرة عامة على الفصل

يا جماعة، بعد ما اتعلمنا كل حاجة عن FortiGate وإزاي نكونه ونأمن بيه الشبكة، دلوقتي جه وقت نتكلم عن أهم جزء لأي مهندس شبكات: استكشاف الأخطاء وإصلاحها (Troubleshooting). مهما كنت خبير، المشاكل بتحصل، والمهم إنك تعرف إزاي تلاقي المشكلة وتحلها بسرعة.

في الفصل ده، هنتعلم إزاي نستخدم أدوات التشخيص القوية اللي FortiGate بيوفرها في سطر الأوامر (CLI) عشان نكتشف المشاكل ونحلها. هنتكلم عن أوامر زي `diagnose debug flow` اللي بتوريك مسار الحزم جوه الجهاز، و `diagnose sniffer packet` اللي بتخليك تشوف الحزم وهي بتعدي على الواجهات. كمان، هنعرف إزاي نستخدم الـ Packet Capture عشان نحلل الترافيك بشكل أعمق.

الفصل ده هيخليك محترف في التعامل مع مشاكل FortiGate، وهيديك الثقة إنك تقدر تحل أي مشكلة تقابلك. يلا بينا نخش في التفاصيل!

## 10.1 مقدمة إلى استكشاف الأخطاء وإصلاحها (Introduction to Troubleshooting)

### 10.1.1 منهجية استكشاف الأخطاء وإصلاحها

عشان تحل أي مشكلة بشكل فعال، لازم تمشي على منهجية منظمة. متشتغلش عشوائي. دي منهجية بسيطة ممكن تمشي عليها:

1.  **تحديد المشكلة (Identify the Problem):**
    -   إيه اللي مش شغال؟ مين المتأثر؟ إمتى بدأت المشكلة؟ هل حصل أي تغييرات مؤخراً؟
    -   اجمع أكبر قدر ممكن من المعلومات من المستخدمين أو من أنظمة المراقبة.

2.  **تحديد النطاق (Establish a Theory of Probable Cause):**
    -   بناءً على المعلومات اللي جمعتها، حط فرضيات محتملة لسبب المشكلة.
    -   هل المشكلة في الشبكة؟ في الـ FortiGate؟ في السيرفر؟ في المستخدم؟

3.  **اختبار الفرضية (Test the Theory):**
    -   اختبر الفرضيات بتاعتك واحدة واحدة. ابدأ بالأسهل والأكثر احتمالاً.
    -   مثلاً: لو المشكلة في الوصول لموقع، جرب تعمل Ping، بعدين Traceroute، بعدين شوف الـ Firewall Policy.

4.  **وضع خطة عمل (Establish a Plan of Action):**
    -   بعد ما تحدد السبب المحتمل، حط خطة عشان تحل المشكلة. فكر في الخطوات اللي هتاخدها، وإيه اللي ممكن يحصل لو الخطة فشلت.

5.  **تنفيذ الخطة (Implement the Solution):**
    -   نفذ الخطة بتاعتك. سجل كل خطوة بتعملها عشان لو احتجت ترجع فيها.

6.  **التحقق من الحل (Verify Full System Functionality):**
    -   تأكد إن المشكلة اتحلت تماماً، وإن مفيش أي آثار جانبية للحل بتاعك.
    -   اسأل المستخدمين المتأثرين، وجرب بنفسك.

7.  **توثيق الحل (Document the Solution):**
    -   سجل المشكلة، سببها، والحل اللي عملته. ده هيساعدك لو المشكلة دي حصلت تاني، وهيساعد زمايلك.

### 10.1.2 أدوات التشخيص في FortiGate (CLI)

FortiGate بيوفر مجموعة كبيرة من أوامر الـ `diagnose` اللي بتخليك تشوف اللي بيحصل جوه الجهاز بشكل مفصل جداً. دي أهم الأوامر اللي هنستخدمها:

-   `diagnose debug flow`
-   `diagnose sniffer packet`
-   `get system status`
-   `get router info routing-table all`
-   `diagnose sys top`

**مقارنة بـ Cisco Troubleshooting:**

Cisco عندها نفس المنهجية، وبتوفر أوامر `show` و `debug` قوية جداً. مثلاً، `show ip route` زي `get router info routing-table all`، و `debug ip packet` زي `diagnose debug flow`. المفهوم واحد: استخدم الأدوات المتاحة عشان تشوف إيه اللي بيحصل في الشبكة والجهاز.

## 10.2 تتبع مسار الحزم (diagnose debug flow)

### 10.2.1 مفهوم `diagnose debug flow`

الأمر ده هو أهم أمر في الـ Troubleshooting على FortiGate. بيوريك بالظبط إزاي FortiGate بيتعامل مع كل حزمة (Packet) بتعدي من خلاله. بيوريك مسار الحزمة من أول ما تدخل FortiGate لحد ما تخرج منه، وإيه السياسات اللي طبقت عليها، وإيه المشاكل اللي حصلت.

### 10.2.2 استخدام `diagnose debug flow`

**الخطوة 1: تفعيل الـ Debug:**

قبل ما تبدأ، لازم تفعل الـ Debug وتحدد الفلاتر اللي عايزها عشان متطلعش معلومات كتير ملهاش لازمة.

```
# عشان تشوف كل الأوامر المتاحة
diagnose debug flow ?

# عشان تفعل الـ Debug وتحدد الفلاتر
diagnose debug flow filter clear
diagnose debug flow filter addr <Source_IP_Address> # أو Destination_IP_Address
diagnose debug flow filter port <Port_Number>
diagnose debug flow filter proto <Protocol_Number> # 6 for TCP, 17 for UDP
diagnose debug flow show console enable
diagnose debug flow show function enable
diagnose debug flow show iprope enable
diagnose debug flow trace start <Number_of_Packets> # عدد الحزم اللي عايز تشوفها

# مثال: تتبع ترافيك HTTP من IP معين
diagnose debug flow filter clear
diagnose debug flow filter addr 192.168.1.100
diagnose debug flow filter port 80
diagnose debug flow filter proto 6
diagnose debug flow show console enable
diagnose debug flow show function enable
diagnose debug flow show iprope enable
diagnose debug flow trace start 100
```

**الخطوة 2: عمل الترافيك اللي عايز تتبعه:**

بعد ما تفعل الـ Debug، اعمل الترافيك اللي عايز تتبعه (مثلاً: حاول تفتح الموقع اللي فيه مشكلة، أو اعمل Ping).

**الخطوة 3: إيقاف الـ Debug:**

بعد ما تخلص، لازم توقف الـ Debug عشان متستهلكش موارد الجهاز.

```
diagnose debug flow trace stop
diagnose debug flow show console disable
diagnose debug flow show function disable
diagnose debug flow show iprope disable
```

### 10.2.3 تحليل مخرجات `diagnose debug flow`

المخرجات بتكون مفصلة جداً، لكن ركز على الأجزاء دي:

-   **`id=`:** رقم تعريف الحزمة.
-   **`dir=`:** اتجاه الحزمة (inbound/outbound).
-   **`src=` و `dst=`:** عنوان الـ Source والـ Destination.
-   **`proto=` و `sport=` و `dport=`:** البروتوكول والـ Source Port والـ Destination Port.
-   **`policy_id=`:** رقم الـ Firewall Policy اللي الحزمة طابقتها.
-   **`drop` أو `deny`:** لو الحزمة تم إسقاطها أو رفضها، هيقولك السبب.

**مثال لمخرجات `diagnose debug flow`:**
```
id=20085 trace_id=1 func=print_pkt_info line=1840 msg="npu_handle_cap_off: packet is offloaded to npu"
id=20085 trace_id=1 func=__ip_session_run_main line=2574 msg="enter fast path"
id=20085 trace_id=1 func=ip_session_run_main line=2593 msg="ip_session_run_main: offloaded"
```

**مقارنة بـ Cisco:**

في Cisco، ممكن تستخدم `debug ip packet` أو `debug ip flow`، لكنها مش بنفس تفصيل `diagnose debug flow` في FortiGate. Cisco عندها `packet tracer` على الـ ASA اللي بيوفر وظيفة مشابهة لتتبع مسار الحزمة.

## 10.3 التقاط الحزم (Packet Capture)

### 10.3.1 مفهوم Packet Capture

الـ Packet Capture (أو Sniffer) بيسمحلك تلتقط الحزم اللي بتعدي على واجهة معينة في FortiGate، وتشوف محتواها. ده مفيد جداً لما تكون عايز تشوف إيه اللي بيحصل بالظبط على مستوى الشبكة، أو لما تكون عايز تحلل مشكلة معقدة.

### 10.3.2 استخدام `diagnose sniffer packet`

**الخطوة 1: تفعيل الـ Sniffer:**

```
# عشان تشوف كل الأوامر المتاحة
diagnose sniffer packet ?

# عشان تلتقط الحزم
diagnose sniffer packet <interface> <'filter'> <verbose> <count>

# interface: الواجهة اللي عايز تلتقط عليها (مثلاً: port1, wan1, any)
# filter: فلتر عشان تحدد نوع الترافيك اللي عايز تلتقطه (زي tcp port 80, host 192.168.1.1)
# verbose: مستوى التفاصيل (1: رؤوس الحزم، 2: رؤوس الحزم والـ IP، 3: رؤوس الحزم والـ IP والـ Ethernet، 4: كل حاجة)
# count: عدد الحزم اللي عايز تلتقطها

# مثال: التقاط 100 حزمة HTTP على واجهة port1
diagnose sniffer packet port1 'tcp port 80' 4 100

# مثال: التقاط ترافيك من IP معين على أي واجهة
diagnose sniffer packet any 'host 192.168.1.100' 4 0 # 0 يعني عدد لا نهائي
```

**الخطوة 2: عمل الترافيك اللي عايز تلتقطه:**

بعد ما تفعل الـ Sniffer، اعمل الترافيك اللي عايز تلتقطه.

**الخطوة 3: إيقاف الـ Sniffer:**

لو استخدمت `count`، الـ Sniffer هيقف لوحده. لو استخدمت `0`، لازم توقفه يدوياً بـ `Ctrl+C`.

### 10.3.3 تحليل مخرجات `diagnose sniffer packet`

المخرجات بتوريك كل حزمة، الـ Source IP والـ Destination IP، البورتات، والبروتوكولات. لو استخدمت `verbose 4`، هتشوف محتوى الحزمة كمان.

**مثال لمخرجات `diagnose sniffer packet`:**
```
interfaces=[port1] filters=[host 192.168.1.100] 
1.000000 192.168.1.100.1234 -> 8.8.8.8.53: udp 40
1.000100 8.8.8.8.53 -> 192.168.1.100.1234: udp 56
```

**مقارنة بـ Cisco:**

Cisco عندها أوامر `monitor capture` على الـ Routers والـ ASA اللي بتوفر وظيفة مشابهة لالتقاط الحزم. ممكن تحفظ الـ Capture في ملف وتفتحه ببرنامج زي Wireshark لتحليل أعمق. FortiGate أيضاً بيسمحلك تحفظ الـ Capture في ملف.

## الخلاصة

في الفصل ده، اتعلمنا إزاي نكون محترفين في استكشاف الأخطاء وإصلاحها على FortiGate. عرفنا منهجية منظمة لحل المشاكل، واتعمقنا في أهم أدوات التشخيص في الـ CLI زي `diagnose debug flow` و `diagnose sniffer packet`.

دلوقتي أنت بقيت قادر على تحديد المشاكل، تتبع مسار الحزم، وتحليل الترافيك عشان توصل لسبب المشكلة وتحلها بسرعة وكفاءة. الفصل ده هو مفتاحك عشان تبقى مهندس شبكات FortiGate متمكن.

يلا بينا على الفصل اللي جاي عشان نتكلم عن FortiManager و FortiAnalyzer، اللي بيساعدوك تدير وتراقب عدد كبير من أجهزة FortiGate بشكل مركزي.

---

*تأليف: Manus AI*
*تاريخ الإنشاء: ديسمبر 2024*

