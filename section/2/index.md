# فصل دوم

در این بخش به تشریح نماهای مختلف از جمله منطقی، استقرار، پیاده‌سازی و غیره پرداخته خواهد شد. در انتها نیز راه‌حل‌های موجود و تکنولوژی‌های متناسب با سامانۀ فیلم‌پلاس بررسی می‌شود.

## نمای منطقی

بررسی نمای منطقی معماری فیلم‌پلاس به کمک نمودار ترتیبی نمایش داده شده در شکل‌های (2) و (3) و (4) انجام شده است. جهت سهولت در فهم، نمودار ترتیبی به سه قسمت تقسیم شده است:

1. نمودار شکل (2) شامل عملیات کاربری و اشتراک است؛ در این نمودار ابتدا ترتیب ثبت‌نام کاربر در سامانه و سپس ورود ایشان نشان داده شده است. سپس یک عملیات خرید اشتراک نمایش داده شده که در آن درخواست خرید اشتراک به پایگاه دادۀ اشتراک، توسط سرویس اطلاعات کاربری صورت می‌پذیرد.

<figure>
    <img id="inlineImg" class="inlImg" onclick="displayImg(this);" src="https://github.com/alihanifi/filmplusarc/blob/main/images/User%20Sequence%20Diagram.png?raw=true" width="400">
    <figcaption>شکل (2): نمودار ترتیبی سامانۀ فیلم‌پلاس - سرویس کاربری</figcaption>
</figure>

2. نمودار شکل (3) شامل عملیات آپلود ویدئو است؛ در این نمودار ترتیب یک عملیات آپلود فایل ویدئویی نشان داده شده است که در آن ابتدا کاربر درخواست خود را به سرویس کد‌گذاری می‌دهد و این سرویس، عملیات مورد نیاز از جمله فشرده‌سازی، تبدیل فرمت و غیره را انجام می‌دهد و فایل را برای ذخیره به CDN ارسال می‌کند. همزمان با این عملیات، اطلاعات متای فیلم نیز در پایگاه‌ دادۀ مربوطه ذخیره می‌گردد.

<figure>
    <img id="inlineImg" class="inlImg" onclick="displayImg(this);" src="https://github.com/alihanifi/filmplusarc/blob/main/images/Video%20Upload%20Sequence%20Diagram.png?raw=true" width="400">
    <figcaption>شکل (3): نمودار ترتیبی سامانۀ فیلم‌پلاس - آپلود ویدئو</figcaption>
</figure>

3. نمودار شکل (4) شامل عملیات جستجو و نمایش فیلم است؛ در این نمودار ابتدا ترتیب یک عملیات جستجو نشان داده شده است که در آن سرویس جستجو اطلاعات موجود در پایگاه‌های دادۀ متا و جستجو ترکیب نموده و سپس به کاربر نمایش می‌دهد. سپس یک ترتیب نمایش ویدئو نشان داده شده است که در آن ابتدا کَش بررسی شده و درصورتیکه فایل‌های مورد نیاز از قبل در کَش وجود داشته باشند، همان برای نمایش بافِر شده و به کاربر ارائه می‌شود؛ درصورتیکه فایل‌ها در کَش موجود نباشند، سرویس ویدئو درخواستی مبنی بر انتقال فایل از S3 به کَش را برای CDN ارسال می‌کند و CDN باتوجه به موقعیت جغرافیایی کاربر و همچنین نحوۀ ذخیره‌سازی فایل‌ها، یکی از مخزن‌های S3 را معرفی نموده و سپس فایل به کَش انتقال پیدا می‌کند. ذکر این نکته حائز اهمیت است که تمامی عملیات انجام شده توسط CDN و S3 به‌صورت فارغ از سامانۀ فیلم‌پلاس انجام می‌شود و فقط به‌عنوان یک سرویس ذخیرۀ فایل از آن استفاده خواهد شد. پس از انتقال فایل به کَش، مجددا فایل برای نمایش آماده‌سازی می‌شود.

<figure>
    <img id="inlineImg" class="inlImg" onclick="displayImg(this);" src="https://github.com/alihanifi/filmplusarc/blob/main/images/Video%20Watch%20Sequence%20Diagram.png?raw=true" width="400">
    <figcaption>شکل (4): نمودار ترتیبی سامانۀ فیلم‌پلاس - جستجو و نمایش ویدئو</figcaption>
</figure>

## نمای پیاده سازی

بررسی نمای پیاده‌سازی معماری فیلم‌پلاس به کمک نمودار مولفۀ نمایش داده شده در شکل (5) انجام شده است. اجزای این نمودار عبارتند از:
* سرویس اطلاعات کاربری: این سرویس عمدتاً مسئول احراز هویت، دسترسی‌ها، اشتراک‌ها و سایر اطلاعات مرتبط با پروفایل کاربران است. این سرویس، با استفاده از یک پایگاه داده مانند MySQL یا PostgreSQL داده‌های کاربران را حفظ می‌کند. برای چنین مجموعه‌ای از داده‌ها، به ویژگی‌های ACID نیاز داریم و به همین علت، RDBMS  گزینۀ مناسبی است.
* سرویس اشتراک: این سرویس همانطور که از نامش پیداست، مسئول مدیریت اشتراک کاربران است و به‌علت اینکه تراکنش‌های آن بسیار بالاست، مانند سرویس اطلاعات کاربری برای آن نیز RDBMS استفاده شده است.
* سرویس ویدئو: این سرویس مسئول انتقال ویدئو از سرورها به کاربر است. برای این منظور، از یک RDBMS جهت انجام عملیات اَبَر داده‌های ویدئوها استفاده می‌شود و همچنین از یک کَش جهت افزایش سرعت و همچنین کاهش فشار پایگاه‌های داده استفاده خواهد شد. جهت استفاده از کَش می‌توان از ساختارهایی مانند Redis استفاده کرد.
* سرویس کدگذاری: این سرویس مسئولیت‌های زیر را برعهده دارد:
    * چک کردن و تصحیح کیفیت ویدئوها (مثلاً افت فریم و موارد مشابه)
    * فشرده‌سازی و ایجاد فرمت‌های مختلف از ویدئو (مثلاً MKV و MP4 و غیره)
    * ایجاد نمودن ویدئو با کیفیت‌های مختلف (مثلاً 360p و 720p و غیره)
* همچنین کاربران ارشدی که دسترسی برای تغییر و یا ایجاد ویدئو دارند، می‌توانند عملیات خود را از طریق این سرویس انجام دهند. هنگامیکه یک کاربر ویدئوی جدیدی ایجاد می‌کند، این ویدئو پس از چک شدن، فشرده‌سازی و ایجاد فرمت‌ها و سپس ایجاد کیفیت‌های مختلف در یک مخزن دادۀ داخلی مثل سطل S3  ذخیره شده و سپس اَبَرداده‌های آن در پایگاه داده ذخیره می‌شوند.
* سرویس جستجو: این سرویس به کاربران امکان می‌دهد تا با استفاده از اَبَرداده‌هایی مانند عنوان فیلم، خلاصه، نام بازیگران، سبک فیلم و غیره، ویدیویی را جستجو کنند. این سرویس به یک پایگاه داده است که جستجوی متنی کامل را پشتیبانی نموده و می‌توان از Solr یا Elasticsearch برای آن استفاده کرد.
* سرویس کاربران داخلی: این سرویس مسئول مدیریت کاربران ویژه مانند مدیران، ذینفعان و کاربران مدیریت محتواست.
* شبکۀ تحویل محتوا (CDN ): این سرویس، در واقع یک سرویس خارجی اَبری بوده و یک شبکۀ توزیع جغرافیایی از سرورهاست که محتوا را از نزدیکترین سرور از نظر جغرافیایی به کاربران ارائه می‌دهد. این امر تأخیر و تعداد پرش‌های شبکه را کاهش داده و در نتیجه عملکرد تحویل محتوا را بهبود می‌بخشد. استفاده از CDN برای انتقال ویدئو به کاربرد، کارایی را افزایش می‌دهد زیرا ویدئوها از نزدیکترین مکان به کاربران ارائه می‌شود.
* لود بالانسر: در این سیستم دو عدد لود بالانسر پیش‌بینی شده است که در واقع مجموعه‌ای از سرورها و شبکه‌های داخلی مربوطه است که درخواست‌ها و پاسخ‌های کاربران را مدیریت می‌کند. استفاده از لود بالانسر باعث می‌شود تا از ظرفیت سرورها و سرویس‌ها به‌درستی استفاده شود و هیچ‌کدام از آن‌ها دچار اضافه بار نگردد.

<figure>
    <img id="inlineImg" class="inlImg" onclick="displayImg(this);" src="https://github.com/alihanifi/filmplusarc/blob/main/images/Component%20Diagram.png?raw=true" width="400">
    <figcaption>شکل (5): نمودار مولفۀ سامانۀ فیلم‌پلاس</figcaption>
</figure>

## نمای فیزیکی

بررسی نمای فیزیکی سامانۀ فیلم‌پلاس به کمک نمودار پیاده‌سازی نمایش داده شده در شکل (6) انجام شده است. در این شکل عناصر اصلی به چهار دسته تقسیم می‌شوند:

1. سیستم‌ها سرورهای لود بالانسینگ: شامل فایروال و وب سرورها برای پاسخگویی به کلاینت‌ها.
2. سیستم‌های اجرایی: شامل مولفه‌های اجرایی و اینترفیس‌ها.
3. سیستم‌های نگهداری داده‌ها: شامل انواع پایگاه‌های داده.
4. سیستم‌های خارجی: شامل یک سرویس خارجی جهت ذخیرۀ فایل‌ها (Amazon S3) و یک سرویس خارجی CDN (Cloudfront).

درصورتیکه این موارد در رابطه با استفاده از این سامانه مفروض باشند:

1. تعداد کاربران فعال در طی یک روز برابر است با 50 میلیون نفر باشد؛
2. متوسط تعداد ویدئوی نمایش داده شده برای هر کاربر برابر با 5 ویدئو باشد؛
3. متوسـط سایز هر ویدئو 500 مگابایت باشـد؛
4. متوسط تعداد آپلود ویدئو 500 عدد باشـد؛

می‌توان مقادیر زیر را در رابطه با پهنای باند و حجم مورد نیاز محاسبه کرد:

* تعداد ویدئوی مشاهده شده طی یک روز: 50 میلیون نفر × 5 ویدئو = 250 میلیون ویدئو
* حجم ویدئوی خروجی طی یک روز: 250 میلیون ویدئو × 500 مگابایت = 125 پتابایت
* حداقل پهنای باند خروجی مورد نیاز: 5/14 گیگابایت بر ثانیه
* تعداد ویدئوی آپلود شده طی یک روز: 500 ویدئو × 500 مگابایت = 250 گیگابایت
* حداقل پهنای باند ورودی مورد نیاز: 9/2 مگابایت بر ثانیه
* حداقل فضای مورد نیاز برای 5 سال: 250 گیگابایت × 5 سال × 365 روز = 25/456 ترابایت

با توجه به این محاسبات، می‌توان سخت‌افزار و سرویس‌های متناسب با کارکرد سامانه را انتخاب نمود.

<figure>
    <img id="inlineImg" class="inlImg" onclick="displayImg(this);" src="https://github.com/alihanifi/filmplusarc/blob/main/images/Deployment%20Diagram.png?raw=true" width="400">
    <figcaption>شکل (6): نمودار پیاده‌سازی سامانۀ فیلم‌پلاس</figcaption>
</figure>

## نمای فرایند

بررسی نمای فرایند سامانۀ فیلم‌پلاس به کمک نمودارهای فعالیت نمایش داده شده در شکل‌های (7) و (8) و (9) انجام شده است. همانند آنچه در قسمت نمای منطقی بیان شد، تقسیم‌بندی نمودارهای فعالیت به قسمت‌های مختلف جهت فهم بهتر ضروری است. در این قسمت فقط سه فرایند کلان سامانه آورده شده‌اند:

1. فرایند ثبت نام و ورود
2. فرایند تماشای ویدئو
3. فرایند خرید اشتراک

البته فرایندهای بسیار دیگری را نیز می‌توان برای این سامانه متصور شد.

<figure>
    <img id="inlineImg" class="inlImg" onclick="displayImg(this);" src="https://github.com/alihanifi/filmplusarc/blob/main/images/User%20Activity%20Diagram.png?raw=true" width="400">
    <figcaption>شکل (7): نمودار فعالیت سامانۀ فیلم‌پلاس - فرایند ثبت نام و ورود</figcaption>
</figure>

<figure>
    <img id="inlineImg" class="inlImg" onclick="displayImg(this);" src="https://github.com/alihanifi/filmplusarc/blob/main/images/Subscription%20Activity%20Diagram.png?raw=true" width="400">
    <figcaption>شکل (8): نمودار فعالیت سامانۀ فیلم‌پلاس - فرایند خرید اشتراک</figcaption>
</figure>

<figure>
    <img id="inlineImg" class="inlImg" onclick="displayImg(this);" src="https://github.com/alihanifi/filmplusarc/blob/main/images/Watch%20Video%20Activity%20Diagram.png?raw=true" width="400">
    <figcaption>شکل (9): نمودار فعالیت سامانۀ فیلم‌پلاس - فرایند تماشای ویدئو</figcaption>
</figure>

## ابزارها و فناوری‌ها

ابزارها و فناوری‌های مورد استفاده در قسمت‌های قبلی به‌صورت خلاصه بیان شدند. در این قسمت به تشریح بیشتر این ابزارها و فناوری‌ها پرداخته خواهد شد.

* استفاده از کَش: در این سامانه از یک کش در حافظه به‌منظور کاهش دفعات مراجعه به پایگاه داده استفاده می‌شود. از جمله تکنیک‌ها و ابزارهای کشینگ می‌توان به ساختارهای Redis و Memcached  اشاره کرد که داده‌ها را از پایگاه داده به صورت جفت مقدار کلید ذخیره می‌کنند. زمانیکه کاربر بخواهد فایلی را مشاهده کند، قبل مراجعه به پایگاه داده، بررسی می‌کنیم که آیا داده‌ها در حافظۀ پنهان وجود دارند یا خیر؟ درصورت وجود، با دور زدن یک مراجعه به پایگاه داده، مقدار را از کَش برمی‌گردانیم. درغیراینصورت، اگر داده‌ها در کَش وجود نداشته باشند، به پایگاه داده مراجعه نموده و داده‌ها را دریافت می‌کنیم. بنابراین درخواست‌های بعدی به پایگاه داده نمی‌رسند و داده‌ها را از حافظه پنهان دریافت نمی‌کنند.
* استفاده از CDN: استفاده از CDN باعث می‌شود تا محتوا از نزدیکترین سرور از نظر جغرافیایی به کاربران ارائه شود. این امر تأخیر و تعداد پرش‌های شبکه را کاهش داده و در نتیجه عملکرد تحویل محتوا را بهبود می‌بخشد. استفاده از CDN برای ارائۀ ویدئو، کارایی را افزایش می‌دهد زیرا ویدئوها از مکان نزدیک به کاربران ارائه می‌شوند و تعداد پرش‌های شبکه کاهش می‌یابد. همچنین CDN می‌تواند ویدیو را برای تحویل سریع‌تر در حافظۀ کَش مهیا کند.
* استفاده از لود بالانسر: در این معماری از دو لود بالانسر جلویی و پشتی استفاده شده است. برای افزایش ظرفیت سرویس، می‌توانیم سرورهای برنامۀ بیشتری را پشت لود بالانسر اضافه کنیم. چنین معماری که به‌نام مقیاس‌پذیری افقی شناخته می‌شود، باعث می‌شود تا هر سرویس به‌طور مستقل قابل مقیاس‌پذیری باشد. بنابراین استفاده از چنین روشی باعث می‌شود تا در آینده مشکلی از لحاظ مقیاس‌پذیری بوجود نیاید.
* استفاده از پایگاه دادۀ جداگانه برای جستجو: پایگاه دادۀ جستجو، که جستجوی متنی کامل را پشتیبانی می‌کند، می‌تواند در چندین سرور تقسیم شود. استفاده از ابزارهایی مثل Elasticsearch منجر به بهبود زمان اجرای کوئری‌ها کمک می‌کند.
