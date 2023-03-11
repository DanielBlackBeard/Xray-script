# اسکریپت ساخت/مدیریت وب Xray-TLS+
## فهرست مطالب
[1. ویژگی های اسکریپت](#ویژگی های اسکریپت)

[2. موارد احتیاط](#注意事项)

[3. شرح زمان نصب](#安装时长说明)

[4. دستورالعمل های اسکریپت](#脚本使用说明)

[5. اجرای اسکرین شات](#运行截图)

[6. توضیحات وب سایت جعلی](#伪装网站说明)

[7. درباره TLS handshake، اثر انگشت TLS و ALPN](#درباره tls handshake tls اثر انگشت و alpn)

[8. درباره gRPC و WebSocket] (#درباره gRPC و WebSocket)

[9. محل نصب](#安装位置)

[10. لیست وابستگی](#依赖列表)

[11. توجه] (#یادداشت)

## ویژگی های اسکریپت
1. پشتیبانی از (VLESS/VMess)-(TCP/gRPC/WebSocket)-(XTLS/TLS) + ساخت/مدیریت وب، پشتیبانی از همزیستی چندین پروتکل
2. گزینه های نصب چند نسخه bbr/sharp یکپارچه 
3. پشتیبانی از چندین سیستم (Ubuntu CentOS Debian deepin fedora ...)
4. پشتیبانی از مجموعه دستورالعمل های متعدد (x86 x86_64 arm64 ...)
5. پشتیبانی از سرور ipv6only (نیاز به تنظیم dns64 توسط خودتان)
6. عملکرد حذف Alibaba Cloud Shield و Tencent Cloud Shield را یکپارچه کنید (فقط برای سرورهای Alibaba Cloud و Tencent Cloud معتبر است)
7. از Nginx به عنوان یک سرویس وب سایت استفاده کنید
8. از Xray به عنوان پیش تقسیم کننده استفاده کنید
9. از acme.sh برای درخواست خودکار/تجدید گواهینامه های نام دامنه استفاده کنید
10. از گزینه ساخت یک دیسک شبکه شخصی به عنوان یک صفحه وب استتار شده پشتیبانی کنید
## موارد احتیاط
1. این اسکریپت نیاز به یک نام دامنه دارد که در سرور حل شود (پشتیبانی از cdn)
2. نصب این اسکریپت زمان زیادی می برد، به **[شرح زمان نصب](#شرح زمان نصب)** مراجعه کنید.
3. این اسکریپت برای کاربران VPS شخصی طراحی شده است، برای صاحبان میزبان مناسب نیست (این اسکریپت عملکردهایی مانند مدیریت چند کاربر/آمار ترافیک ندارد).
4. توصیه می شود از این اسکریپت در یک سیستم تمیز (کنسول VPS - سیستم ریست) استفاده کنید.
## شرح زمان نصب
زمان نصب این اسکریپت نسبتا طولانی است (**[مرجع زمان نصب](#مرجع زمان نصب)**) دلیل آن را [اینجا] (#چرا زمان نصب اسکریپت اینقدر طولانی است) ببینید.
این اسکریپت برای استفاده طولانی مدت پس از یک بار نصب مناسب است و برای ریست کردن مکرر نصب سیستم که زمان زیادی از شما می گیرد مناسب نیست. اگر نیاز به تغییر پیکربندی و نام دامنه و غیره دارید، گزینه های مربوطه در رابط مدیریت وجود دارد.
در صورت نیاز به نصب سریع، توصیه می شود اسکریپت های دیگر را در **[Xray-core#Installation](https://github.com/XTLS/Xray-core#Installation)** انتخاب کنید.
### مرجع زمان نصب
فرآیند نصب：

«[ارتقای اجزای سیستم]->[نصب bbr]->[نصب php]->نصب Nginx->نصب Xray-> درخواست گواهی->فایل پیکربندی->[پیکربندی وب سایت جعلی]`
قسمت پیچیده شده توسط «[]» اختیاری است.
**این میانگین زمان نصب یک سرور تک هسته ای 1G است، فقط برای مرجع:**
|پروژه|مدت|
|-|-|
|نرم افزار نصب شده را ارتقا دهید|0-10 دقیقه|
|ارتقا سیستم|10-20 دقیقه|
|نصب bbr|0-3 دقیقه|
|نصب php|Centos8 (کرنل gcc8.3 4.18): 20-60 دقیقه|
||Ubuntu20.10 (هسته gcc10.2 5.11-rc3): 15-20 دقیقه|
||Debian10 (کرنل gcc8.3 4.19): 10-15 دقیقه|
| نصب Nginx|13-15 دقیقه|
|Xray را نصب کنید|<نیم دقیقه|
|گواهی درخواست|1-2 دقیقه|
|پروفایل|<100ms|
| پیکربندی وب سایت جعلی|Nextcloud: 1-3 دقیقه|
||Cloudreve: 1-2 دقیقه|
### چرا نصب اسکریپت اینقدر طول می کشد؟
سه دلیل برای طولانی‌تر شدن زمان نسبت به دیگر اسکریپت‌ها وجود دارد:
```
1. یکپارچه عملکرد نصب bbr
2. عملکرد به روز رسانی سیستم و بسته نرم افزاری را یکپارچه کنید
3. (دلیل اصلی) Nginx و php اسکریپت از کد منبع کامپایل می شوند، اسکریپت های دیگر معمولاً مستقیماً برنامه باینری را دریافت می کنند.
```
دلایل اصلی اتخاذ فرم کامپایل شده عبارتند از:
```
1. آسان برای مدیریت
2. سازگاری آسان با سیستم های مختلف
```
مزایای کامپایل نسبت به نصب مستقیم باینری ها عبارتند از:
```
1. راندمان عملیاتی بالا (بهینه سازی -O3 هنگام کامپایل استفاده می شود)
2. نسخه نرم افزار جدید است (می توانید این اسکریپت را با نسخه های دیگر اسکریپت Nginx مقایسه کنید)
```
عیب آن این است که زمان زیادی برای کامپایل نیاز دارد
## دستورالعمل های اسکریپت
### 1. گواهینامه های wget && ca-را نصب کنید
سیستم پایه دبیان (از جمله اوبونتو، دبیان، عمیق):
``باش
apt --no-install-commends -y install wget ca-certificates || (apt update && apt --no-install-commends -y install wget ca-certificates)
```
سیستم پایه ردهت (شامل CentOS، فدورا):
``باش
dnf -y نصب wget ca-certificates || yum -y نصب wget ca-certificates
```
### 2. دریافت/به‌روزرسانی اسکریپت
``باش
wget -O Xray-TLS+Web-setup.sh https://github.com/kirin10000/Xray-script/raw/main/Xray-TLS+Web-setup.sh
```
### 3. اسکریپت را اجرا کنید
``باش
bash Xray-TLS+Web-setup.sh
```
### 4. نصب را طبق دستور اسکریپت کامل کنید
## اسکرین شات را اجرا کنید
<div>
     <img width="400" src="https://github.com/kirin10000/Xray-script/blob/main/image/menu.jpg">
</div>
<div>
     <img width="600" src="https://github.com/kirin10000/Xray-script/blob/main/image/protocol.jpg">
</div>
## توضیحات وب سایت بالماسکه

### نقش وب سایت استتار

این وب سایت یک وب سایت است که با نام دامنه شما ساخته شده است، پس از اتمام ساخت، می توانید مستقیماً نام دامنه خود را در مرورگر وارد کنید تا از آن بازدید کنید.

تمام ترافیکی که با Xray پروکسی می کنید به عنوان ترافیک این وب سایت پنهان می شود.

توجه داشته باشید که وب سایت های جعلی نوشدارویی نیستند.طبق تجربه برخی افراد، تا زمانی که ترافیک ماهانه شما از حد معینی فراتر رود، اپراتور شما را مسدود می کند، مهم نیست که وب سایت جعلی شما چیست. یعنی حتی اگر اصلا پروکسی نکنید اما به طور معمول از وب سایت خود بازدید کنید و بازدید زیادی داشته باشید، ممکن است مسدود شوید.
### تظاهر به انتخاب یک وب سایت
ویژگی های رایج ترافیک با استفاده از پروکسی Xray خود ساخته VPS عبارتند از **تک نقطه**، **ترافیک زیاد**، **طولانی مدت**، **ویژگی های اثر انگشت GO-TLS**، **دسترسی یکسان جنسیت * * و غیره.

* **نکته** به این معنی است که افراد کمی هستند که از آن استفاده می کنند، عموماً فقط خودتان، حتی اگر آن را با دوستان خود به اشتراک بگذارید، عموماً زیاد نخواهد بود.
* **زمان طولانی** نه تنها به زمان طولانی اشاره دارد، بلکه به استفاده هر روز از عامل به مدت یک ماه یا یک سال نیز اشاره دارد.
* **ویژگی اثر انگشت GO-TLS** **با فرض عدم استتار اثر انگشت مرورگر**، می توان از اطلاعات دست دادن TLS قضاوت کرد که کلاینت یک برنامه GO است، برای جزئیات [اینجا] را ببینید (#درباره دست دادن tls tls اثر انگشت و alpn).
* **یکسان بودن ورودی و خروجی** به این معنی است که ترافیک ورودی و خروجی از VPS از نظر زمان و اندازه تقریباً یکسان است، مانند استفاده از پراکسی Xray برای مرور "BiliBili"، ترافیک از "BiliBili" به "VPS (سرور Xray )`، و ترافیک از «VPS» به «Xray Client» تقریباً از نظر زمان و اندازه یکسان است. **یکسان بودن ورودی و خروجی** مشکل رایج همه پراکسی هاست.هنوز راه خوبی برای پنهان کردن آن وجود ندارد، اما چون VPS در سرزمین اصلی قرار ندارد، اگر مورد توجه ویژه قرار نگیرد، به طور کلی سانسور نخواهد شد.

از آنجایی که تمام ترافیکی که توسط Xray پراکسی می‌شود به عنوان ترافیک بازدید از این وب‌سایت پنهان می‌شود، پس ما وب‌سایت را پنهان می‌کنیم تا سعی کنیم وب‌سایتی را با مشخصات ترافیکی مشابه با مشخصات ترافیک پراکسی Xray انتخاب کنیم**.

1. **Cloudreve و Nextcloud**

همه آنها دیسک های شبکه شخصی هستند. دیسک های شبکه شخصی را می توان به عنوان دیسک های شبکه Baidu که با استفاده از VPS خود ساخته شده اند درک کرد. تفاوت این است که فایل ها در VPS ذخیره می شوند و آنها سرپرست دیسک های شبکه هستند.

دیسک های شبکه شخصی بیشترین تطابق را با ویژگی های ذکر شده در بالا دارند، از جمله **تک نقطه**، **ترافیک زیاد**، **ویژگی های اثر انگشت GO-TLS**، **بلند مدت** و غیره. توصیه می شود انتخاب کنید

با توجه به **ویژگی اثرانگشت GO-TLS**، **با فرض عدم استتار اثر انگشت مرورگر**، alpn را روی http/1.1 تنظیم کنید، که می تواند به عنوان یک کلاینت WebDav که در زبان GO پیاده سازی شده است، مبدل شود، [اینجا] را ببینید. جزئیات (# در مورد tls handshake tls اثر انگشت و alpn).

تفاوت های Cloudrev و Nextcloud به شرح زیر است:
||مزایا|معایب|
|-|-|-|
|Nextcloud|توابع بیشتر و قدرتمندتر، کاربران بیشتر|نیاز به نصب php است که زمان زیادی را صرف می‌کند (به **[مرجع زمان نصب](#مرجع زمان نصب)** مراجعه کنید، و همچنین زمان بیشتری را صرف می‌کند. از کلودرو بسیاری از منابع سیستم، بنابراین برای رایانه های کوچک توصیه نمی شود. |
|Cloudreve|سبک، نصب سریع (بدون نیاز به php)، اشغال کمتر منابع سیستم|عملکردهای کمتر، استفاده افراد کمتر|
2. **403 صفحه**

اساساً همه وب سایت های بزرگ دارای پس زمینه وب سایت هستند. به عنوان مثال، وب سایت Bilibili "www.bilibili.com" است. اما زمانی که ویدیو در حال پخش است، فایل ویدیویی توسط URL دیگری ارائه می شود. هنگام پخش ویدیو، روی 'Video Statistics'، جایی که 'Video Host' است، کلیک راست کنید. این نوع URL فقط زمانی محتوا دارد که یک پسوند URL خاص باز شود. اگر URL نادرست باشد، صفحه خطا برگردانده می شود. صفحه 403 به عنوان پس زمینه وب سایت مبدل شده است.

به عبارت دیگر، به عنوان یک صفحه 403 مبدل شده است و هیچ کس نمی داند که آیا وب سایت شما چیزی جز خودتان دارد یا خیر.

3. **وب سایت استاتیک سفارشی**

می توانید کد منبع وب سایت استاتیک خود را قرار دهید که برای Xiaobai توصیه نمی شود.

4. **وب سایت پراکسی معکوس سفارشی**

انتخاب توصیه نمی شود، زیرا پروکسی معکوس اغلب فقط چند فایل html و js است و بیشتر محتوای وب سایت همچنان توسط پس زمینه وب سایت ارائه می شود. ویژگی های جریان بزرگ را برآورده نمی کند.
## درباره دست دادن TLS، اثر انگشت TLS و ALPN
اگرچه TLS یک فناوری رمزگذاری است، در طول فرآیند دست دادن TLS، مقداری اطلاعات متن ساده، از جمله اطلاعات SNI (مشخص شده توسط پارامتر serverName)، ALPN، مجموعه رمز و غیره، ارسال خواهد شد.

استاندارد فعلی TLS الزامات سختگیرانه‌ای در مورد این متن‌های ساده ندارد، بنابراین قالب‌های این اطلاعات متن ساده را می‌توان تحت پیاده‌سازی‌های مختلف TLS متفاوت توصیف کرد و ویژگی‌های متن ساده متفاوت این پیاده‌سازی‌های مختلف TLS، اثر انگشت TLS است.

از طریق اثر انگشت TLS، می‌توانید پیاده‌سازی TLS را که استفاده می‌کنید، مانند TLS کروم، TLS فایرفاکس، و TLS کتابخانه رسمی زبان GO، معکوس کنید.

به طور پیش فرض، Xray از کتابخانه TLS که به طور رسمی توسط زبان GO ارائه شده است، استفاده می کند، که همچنین کتابخانه TLS است که توسط تقریباً همه برنامه های زبان GO استفاده می شود. Xray همچنین می تواند اثر انگشت کروم، فایرفاکس و سافاری را شبیه سازی کند، اما در حال حاضر فقط توسط پروتکل TCP پشتیبانی می شود.

هنگامی که از TCP استفاده می کنید و اثر انگشت مرورگر را پنهان نمی کنید، ALPN می تواند آزادانه تعریف شود. توصیه می شود آن را روی http/1.1 تنظیم کنید، به طوری که مشتری Xray می تواند به عنوان یک کلاینت WebDav که در زبان GO پیاده سازی شده است (مانند **[gowebdav](https://github.com/studio-b12/gowebdav) مبدل شود. **). WebDav یک پروتکل مخصوص شبکه است و پروتکل آن بر اساس HTTP/1.1 است، برای جزئیات بیشتر به: **[WebDav](https://en.wikipedia.org/wiki/WebDAV)** مراجعه کنید.

اگر جعل اثر انگشت مرورگر را انتخاب کنید، پارامتر alpn در پیکربندی کلاینت نامعتبر خواهد بود و ALPN روی h2، http/1.1 ثابت می‌شود. به طور مشابه، هنگام استفاده از WebSocket، ALPN به http/1.1 ثابت می شود؛ در هنگام استفاده از gRPC، ALPN مجبور می شود h2 را اضافه کند. بنابراین، استفاده از WebSocket همچنان می تواند وانمود کند که یک کلاینت WebDav زبان GO است، اما gRPC نمی تواند.
## درباره gRPC و WebSocket
هنگامی که CDN مورد استفاده از gRPC و WebSocket پشتیبانی می کند، چگونه بین این دو انتخاب کنیم؟ تفاوت های اصلی آنها در سه جنبه زیر منعکس می شود: ALPN، تأخیر و عملکرد.

برای ALPN، [اینجا] را ببینید (# about tls handshake tls effect and alpn).

در مورد تأخیر، gRPC همراه با mux است، بنابراین تأخیر کمتر است. توجه داشته باشید که این به تاخیر باز شدن وبسایت اشاره دارد، mux نمی تواند تاخیر بازی را کاهش دهد.

در مورد عملکرد، WebSocket عملکرد قوی تری دارد.اگر دستگاه شما عملکرد ضعیف تری دارد، مانند روتر معمولی خانگی، استفاده از WebSocket سریعتر خواهد بود.
## محل نصب
**Nginx:** `/usr/local/nginx`

**php:** `/usr/local/php`

**Cloudreve:** `/usr/local/cloudreve`

**Xray：** 见 **[Xray-install](https://github.com/XTLS/Xray-install)**
## لیست وابستگی
اسکریپت ممکن است به طور خودکار وابستگی های زیر را نصب کند:
|استفاده |سیستم مبتنی بر دبیان|سیستم مبتنی بر کلاه قرمز|
|-|-|-|
|yumdb set(标记包手动安装)||yum-utils|
|dnf config-manager||dnf-plugins-core|
|setenforce/getenforce(关闭SELinux)|selinux-utils|libselinux-utils|
|ss(检查端口占用)|iproute2|iproute|
|wget|wget|wget|
|curl|curl|curl|
|wget/curl https|ca-certificates|ca-certificates|
|kill/pkill/ps/sysctl/free|procps|procps-ng|
|epel源||epel-release|
|epel源||epel-next-release|
|remi源||remi-release|
|do-release-upgrade(升级系统)|ubuntu-release-upgrader-core||
|unzip|unzip|unzip|
|curl|curl|curl|
|安装bbr内核|linux-base||
|**مبنای تالیف：**|||
|下载源码文件|wget|wget|
|解压tar源码文件|tar|tar|
|解压tar.gz源码文件|gzip|gzip|
|解压tar.xz源码文件|xz-utils|xz|
|gcc|gcc|gcc|
|g++|g++|gcc-c++|
|make|make|make|
|**acme.sh依赖：**|||
||curl|curl|
||openssl|openssl|
||cron|crontabs|
|**openssl را کامپایل کنید：**|||
||perl-base(包含于libperl-dev)|perl-IPC-Cmd|
||perl-modules-5.32(包含于libperl-dev)|perl-Getopt-Long|
||libperl5.32(包含于libperl-dev)|perl-Data-Dumper|
|||perl-FindBin|
|**Nginx را کامپایل کنید：**|||
||libpcre2-dev|pcre2-devel|
||zlib1g-dev|zlib-devel|
|--with-http_xslt_module|libxml2-dev|libxml2-devel|
|--with-http_xslt_module|libxslt1-dev|libxslt-devel|
|--with-http_image_filter_module|libgd-dev|gd-devel|
|--with-google_perftools_module|libgoogle-perftools-dev|gperftools-devel|
|--with-http_geoip_module|libgeoip-dev|geoip-devel|
|--with-http_perl_module||perl-ExtUtils-Embed|
||libperl-dev|perl-devel|
|**php را کامپایل کنید：**|||
||pkg-config|pkgconf-pkg-config|
||libxml2-dev|libxml2-devel|
||libsqlite3-dev|sqlite-devel|
|--with-fpm-systemd|libsystemd-dev|systemd-devel|
|--with-fpm-acl|libacl1-dev|libacl-devel|
|--with-fpm-apparmor|libapparmor-dev||
|--with-openssl|libssl-dev|openssl-devel|
|--with-kerberos|libkrb5-dev|krb5-devel|
|--with-external-pcre|libpcre2-dev|pcre2-devel|
|--with-zlib|zlib1g-dev|zlib-devel|
|--with-bz2|libbz2-dev|bzip2-devel|
|--with-curl|libcurl4-openssl-dev|libcurl-devel|
|--with-qdbm|libqdbm-dev||
|--with-gdbm||gdbm-devel|
|--with-db4|libdb-dev|libdb-devel|
|--with-tcadb|libtokyocabinet-dev|tokyocabinet-devel|
|--with-lmdb|liblmdb-dev|lmdb-devel|
|--with-enchant|libenchant-2-dev/libenchant-dev|enchant-devel|
|--with-ffi|libffi-dev|libffi-devel|
|--enable-gd|libpng-dev|libpng-devel|
|--with-external-gd|libgd-dev|gd-devel|
|--with-webp|libwebp-dev|libwebp-devel|
|--with-jpeg|libjpeg-dev|libjpeg-turbo-devel|
|--with-xpm|libxpm-dev|libXpm-devel|
|--with-freetype|libfreetype6-dev|freetype-devel|
|--with-gmp|libgmp-dev|gmp-devel|
|--with-imap|libc-client2007e-dev|uw-imap-devel|
|--enable-intl|libicu-dev|libicu-devel|
|--with-ldap|libldap2-dev|openldap-devel|
|--with-ldap-sasl|libsasl2-dev|openldap-devel|
|--enable-mbstring|libonig-dev|oniguruma-devel|
|--with-unixODBC,--with-pdo-odbc|unixodbc-dev|unixODBC-devel|
|--with-pdo-dblib|freetds-dev|freetds-devel|
|--with-pdo-pgsql,--with-pgsql|libpq-dev|libpq-devel|
|--with-pspell|libpspell-dev|aspell-devel|
|--with-libedit|libedit-dev|libedit-devel|
|--with-mm|libmm-dev||
|--with-snmp|libsnmp-dev|net-snmp-devel|
|--with-sodium|libsodium-dev|libsodium-devel|
|--with-password-argon2|libargon2-dev|libargon2-devel|
|--with-tidy|libtidy-dev|libtidy-devel|
|--with-xsl|libxslt1-dev|libxslt-devel|
|--with-zip|libzip-dev|libzip-devel|
|php را کامپایل کنید-imagick：|||
||autoconf|autoconf|
||git|git|
||libmagickwand-dev|ImageMagick-devel|
## توجه داشته باشید
1.پیوند به این مقاله(官网)：https://github.com/kirin10000/Xray-script

2.آموزش مرجع：https://www.v2fly.org/config/overview.html https://guide.v2fly.org/ https://docs.nextcloud.com/server/latest/admin_manual/installation/source_installation.html https://docs.cloudreve.org/

3.درخواست گواهی نام دامنه：https://github.com/acmesh-official/acme.sh

4.اسکریپت bbr از：https://github.com/teddysun/across/blob/master/bbr.sh

5.اسکریپت bbr2 از آن می آید：https://github.com/yeyingorg/bbr2.sh (Ubuntu Debian) https://github.com/jackjieYYY/bbr2 (CentOS)

6.اسکریپت bbrplus از آن می آید：https://github.com/chiakge/Linux-NetSpeed

#### این اسکریپت فقط برای ارتباط و یادگیری است، لطفا از این اسکریپت برای انجام کارهای غیرقانونی استفاده نکنید. در جایی که اینترنت غیرقانونی است، چیزهای غیرقانونی با قانون مجازات خواهند شد! !
