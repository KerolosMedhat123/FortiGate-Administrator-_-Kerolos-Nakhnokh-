# FortiGate Administrator _ Kerolos Nakhnokh

مرحباً بكم في (Repository) بتاعتى يجماعة و اللى هنقدم فيها شرحاً كاملاً ومفصلاً لمنهج FortiGate 7.4 Administrator Study Guide. يهدف هذا الشرح إلى تغطية جميع جوانب المنهج بطريقة مبسطة وعميقة، مع ربط المفاهيم بتقنيات الشبكات الأخرى مثل Cisco، مما يجعله مثالياً للتحضير للامتحانات أو لتعميق الفهم العملي و طبعا ده مش مجهود فردى هو مجهود جماعى مع ال AI power اللى قدر يساعدنى الخص و اظبط و انسق حاجه مهمه و ضخمة بالشكل ده.

## محتوى الشرح

تم تقسيم الشرح إلى فصول، كل فصل يغطي جزءاً محدداً من المنهج. تم إعداد هذا المحتوى بأسلوب الحديث معاك كانى بشرحلك فديو، كأنك بتشوف محاضرة تفصيلية لكل موضوع، مع التركيز على الأمثلة العملية والمقارنات و فى شابتر ضيفته فى الاخر هو عبارة عن امثلية عملية فعليا.

### الفصول المتاحة:

1.  **[الفصل الأول: مقدمة إلى FortiGate](Chapter_01_Introduction_to_FortiGate.md)** 
 (Done)
    -   تعريف FortiGate ومكانته في عالم الأمن السيبراني.
    -   مقارنة سريعة بين FortiGate و Cisco ASA.
    -   تاريخ Fortinet وعائلة منتجاتها.

2.  **[الفصل الثاني: التكوين الأساسي](Chapter_02_Basic_Configuration.md)** 
 (Done)
    -   الإعدادات الأولية والوصول إلى FortiGate.
    -   أوضاع التشغيل (NAT/Route, Transparent).
    -   تكوين الواجهات (Interfaces) والـ VLANs.
    -   النطاقات الافتراضية (VDOMs).
    -   إدارة الوصول الإداري، حفظ واستعادة التكوين، وتحديث الـ Firmware.
    -   خدمات FortiGuard.

4.  **[الفصل الثالث: سياسات جدار الحماية (Firewall Policies)](Chapter_03_Firewall_Policies.md)**
    -   مقدمة لسياسات جدار الحماية وأنواعها.
    -   تكوين سياسات جدار الحماية.
    -   تفاصيل مطابقة السياسات.
    -   ملفات تعريف الأمان (Security Profiles).
    -   ترتيب السياسات وتحسينها.
    -   Network Address Translation (NAT).

5.  **[الفصل الرابع: المصادقة (Authentication)](Chapter_04_Authentication.md)**
    -   المستخدمون والمجموعات المحلية.
    -   المصادقة عن بُعد (RADIUS, LDAP, TACACS+).
    -   المصادقة الثنائية (2FA) و FortiToken.
    -   تكامل SAML SSO.
    -   إدارة الضيوف.

6.  **[الفصل الخامس: الشبكات الافتراضية الخاصة (VPNs)](Chapter_05_VPNs.md)**
    -   SSL VPN (الوصول عن بُعد).
    -   IPsec VPN (من موقع إلى موقع).
    -   استكشاف أخطاء VPN وإصلاحها.

7.  **[الفصل السادس: ملفات تعريف الأمان (Security Profiles - UTM Features)](Chapter_06_Security_Profiles.md)**
    -   مكافحة الفيروسات ومكافحة برامج التجسس.
    -   نظام منع التسلل (IPS).
    -   تصفية الويب (Web Filtering).
    -   التحكم في التطبيقات (Application Control).
    -   منع تسرب البيانات (DLP).
    -   أوضاع الفحص (Inspection Modes).

8.  **[الفصل السابع: إدارة النظام والتوافرية العالية (System Management and High Availability)](Chapter_07_System_Management_and_High_Availability.md)**
    -   التسجيل والمراقبة (Logging and Monitoring).
    -   التقارير والتنبيهات.
    -   مفاهيم التوافرية العالية (HA).
    -   تكوين HA (Active-Passive).

9.  **[الفصل الثامن: التوجيه المتقدم والشبكات اللاسلكية (Advanced Routing and Wireless Networking)](Chapter_08_Advanced_Routing_and_Wireless_Networking.md)**
    -   مفاهيم التوجيه المتقدم (OSPF, BGP, PBR).
    -   الشبكات اللاسلكية (FortiAP).

10.  **[الفصل التاسع: ميزات الأمان المتقدمة (Advanced Security Features)](Chapter_09_Advanced_Security_Features.md)**
    -   فحص SSL (SSL Inspection).
    -   الحماية المتقدمة من التهديدات (ATP) و FortiSandbox.
    -   التحكم في الوصول للشبكة (NAC).
    -   التحكم في عرض النطاق الترددي (Bandwidth Control).

11. **[الفصل العاشر: استكشاف الأخطاء وإصلاحها وأدوات التشخيص (Troubleshooting and Diagnostic Tools)](Chapter_10_Troubleshooting_and_Diagnostic_Tools.md)**
    -   مقدمة إلى استكشاف الأخطاء وإصلاحها.
    -   تتبع مسار الحزم (`diagnose debug flow`).
    -   التقاط الحزم (`diagnose sniffer packet`).

12. **[الفصل الحادي عشر: FortiManager و FortiAnalyzer](Chapter_11_FortiManager_and_FortiAnalyzer.md)**
    -   مقدمة إلى FortiManager (الإدارة المركزية).
    -   مقدمة إلى FortiAnalyzer (تجميع السجلات والتقارير).
    -   التكامل بين FortiManager و FortiAnalyzer.

13. **[الفصل الثاني عشر: أمثلة عملية وسيناريوهات تطبيقية (Practical Examples and Scenarios)](Chapter_12_Practical_Examples_and_Scenarios.md)**
    -   تأمين فرع شركة (Branch Office Deployment).
    -   تأمين الوصول عن بُعد (Securing Remote Access).
    -   تأمين تطبيقات الويب (Web Application Security).

14. **[الفصل الثالث عشر: التحضير للامتحان (Exam Preparation)](Chapter_13_Exam_Preparation.md)**
    -   أهمية شهادات Fortinet ومسار شهادات NSE.
    -   نصائح للتحضير للامتحان.
    -   مراجعة سريعة لأهم المواضيع.

## الملفات المتاحة

-   **[FortiGate_Full_Course.md](FortiGate_Full_Course.md)**: يحتوي على الشرح الكامل لجميع الفصول مدمجة في ملف واحد.

نتمنى أن يكون هذا الشرح مفيداً لكم في رحلتكم لتعلم FortiGate!

