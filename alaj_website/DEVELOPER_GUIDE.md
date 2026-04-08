# دليل مطور موقع ALAJ Website

هذا الدليل يشرح القواعد التي اتبعناها في تحويل الصفحة الرئيسية وبلوكاتها إلى بناء أقرب إلى Odoo standard، بحيث يستطيع Website Editor التعرف على البلوكات، تعديل النصوص، تغيير الصور، حذف العناصر، إضافة عناصر جديدة، وترتيب المنيو من الواجهة قدر الإمكان.

الدليل مبني على العمل المنفذ داخل هذه الملفات:

- `data/odoo_homepage.xml`
- `data/alaj_homepage_snippets.xml`
- `data/alaj_layout_override.xml`
- `data/alaj_website_record.xml`
- `data/alaj_standard_menu_test.xml`
- `data/odoo_partners_page.xml`
- `data/odoo_faq_page.xml`

## الهدف العام

أي صفحة أو بلوك في الموقع يجب ألا يكون مجرد HTML ثابت فقط. يجب أن يكون قابلًا للفهم من محرر Odoo عبر:

- قالب صفحة واضح يستدعي `website.layout`.
- أقسام رئيسية تحمل `data-snippet`.
- Snippet template مسجل بنفس الاسم.
- عناصر داخلية مبنية من snippets قياسية قدر الإمكان مثل `s_text_block`, `s_title`, `s_numbers`, `s_card`.
- صور حقيقية عبر `<img>` وليست صورًا مخفية داخل `background-image` فقط.
- منيو مبنية من سجلات `website.menu` وليست روابط ثابتة داخل الهيدر.

## القاعدة الذهبية

لا يكفي أن يظهر التصميم صحيحًا في المتصفح. المطلوب أن يفهمه Website Editor.

إذا كان العنصر جميلًا لكنه غير قابل للتحديد أو التعديل من المحرر، فهو ليس standard عمليًا.

## هيكل الصفحة الصحيح

صفحة Odoo يجب أن تكون داخل template وتستدعي layout القياسي:

```xml
<template id="homepage_template" name="Chemical Industry Homepage">
    <t t-call="website.layout">
        <t t-set="pageName" t-value="'homepage'"/>
        <div id="wrap" class="oe_structure oe_empty">
            ...
        </div>
    </t>
</template>
```

التعليمات:

- استخدم `t-call="website.layout"` حتى يعمل الهيدر والفوتر وبيئة المحرر.
- اضبط `pageName` للصفحة الرئيسية على `homepage`.
- اجعل المحتوى داخل `#wrap`.
- استخدم `oe_structure` على الحاوية التي تحتوي البلوكات القابلة للتحرير.
- لا تضع الصفحة كلها كنص HTML معزول خارج layout.

## تعريف البلوك الرئيسي

كل بلوك رئيسي يجب أن يكون section واضحًا ومعروفًا للمحرر:

```xml
<section class="s_alaj_news alaj-news-section o_colored_level"
         data-name="News"
         data-snippet="s_alaj_news"
         data-vxml="001"
         data-vcss="001">
    ...
</section>
```

التعليمات:

- `data-name` اسم وصفي فقط.
- `data-snippet` هو الأهم، وهو الذي يربط البلوك بقالب snippet.
- `data-vxml` و `data-vcss` تمنع ظهور تحذير `This block is outdated` عندما يكون القالب مسجلًا بشكل صحيح.
- اسم الكلاس المخصص مثل `s_alaj_news` يجب أن يطابق فكرة snippet.
- لا تعتمد على `data-name` فقط، لأنه لا يجعل البلوك standard.

## تسجيل snippets

أي `data-snippet="s_alaj_x"` في الصفحة يجب أن يكون له template بنفس الاسم في `data/alaj_homepage_snippets.xml`.

مثال:

```xml
<template id="s_alaj_news" name="ALAJ News">
    <section class="s_alaj_news alaj-news-section"
             data-name="News"
             data-snippet="s_alaj_news"
             data-vxml="001"
             data-vcss="001">
        ...
    </section>
</template>
```

ثم يتم تسجيله في لوحة البلوكات:

```xml
<t t-snippet="alaj_website.s_alaj_news"
   string="ALAJ News"
   t-thumbnail="/website/static/src/img/snippets_thumbs/s_media_list.svg">
    <keywords>news, events, article, media, updates</keywords>
</t>
```

التعليمات:

- إذا استخدمت `data-snippet` بدون template مسجل، سيظهر البلوك كغير مكتمل أو قد تظهر رسالة outdated.
- إذا استخدمت thumbnail غير موجود، سيظهر 404 مثل مشكلة `s_cards_grid.svg`.
- استخدم thumbnails موجودة فعلًا داخل Odoo مثل `s_three_columns.svg`, `s_cover.svg`, `s_text_image.svg`, `s_media_list.svg`.
- عند إنشاء snippet جديد، أضفه في `alaj_homepage_snippets.xml` وسجله في `alaj_website_snippets`.

## ما فعلناه في الصفحة الرئيسية

حوّلنا الصفحة الرئيسية من HTML مخصص كبير إلى بلوكات مسجلة ومفهومة للمحرر.

البلوكات الحالية في الرئيسية:

- `s_alaj_home_hero`
- `s_alaj_quick_links`
- `s_alaj_partners`
- `s_alaj_solutions_cards`
- `s_alaj_about`
- `s_alaj_founder`
- `s_alaj_store`
- `s_alaj_news`
- `s_alaj_projects`

ملاحظة: قسم Projects أزيل من الصفحة الرئيسية حسب الطلب، لكن snippet الخاص به ما زال موجودًا في `alaj_homepage_snippets.xml` كقالب قابل للاستخدام إذا احتجناه لاحقًا.

## بناء العناصر داخل البلوك

العناصر الداخلية يجب ألا تكون نصوصًا عائمة داخل div فقط. الأفضل استخدام snippets قياسية داخل `oe_structure`.

مثال من الهيرو:

```xml
<div class="oe_structure" data-name="Hero Content">
    <section class="s_text_block" data-snippet="s_text_block" data-name="Hero Badge">
        ...
    </section>
    <section class="s_title" data-snippet="s_title" data-name="Hero Title" data-vcss="001">
        ...
    </section>
    <section class="s_text_block" data-snippet="s_text_block" data-name="Hero Description">
        ...
    </section>
    <section class="s_numbers" data-snippet="s_numbers" data-name="Hero Numbers">
        ...
    </section>
</div>
```

التعليمات:

- النص القصير أو الوصف يوضع داخل `s_text_block`.
- العناوين توضع داخل `s_title`.
- الأرقام والإحصاءات توضع داخل `s_numbers`.
- الأزرار توضع داخل `s_text_block` وتستخدم `btn`.
- البطاقات تستخدم `s_card` أو بنية Bootstrap `card`.
- استخدم `oe_structure` عند الحاجة لمنطقة داخلية تسمح بالتحرير والإضافة.

## الهيرو

الهيرو كان يحتوي هذه العناصر:

- `CHEMICAL & RESEARCH`
- `Leading Chemical Industry Without Limits`
- الوصف الطويل
- زر `Explore Products`
- زر `Learn More`
- `100% CLIENT SATISFACTION`
- `96% MARKET COVERAGE`
- `30+ YEARS EXPERIENCE`

ما فعلناه:

- حوّلنا الشارة إلى `s_text_block`.
- حوّلنا العنوان إلى `s_title`.
- حوّلنا الوصف إلى `s_text_block`.
- حوّلنا الأزرار إلى روابط `btn` داخل `s_text_block`.
- حوّلنا الإحصاءات إلى `s_numbers`.
- أبقينا الاستايل المخصص عبر الكلاسات `alaj-hero-*`.

التعليمات:

- لا تضع النصوص كلها داخل div واحد.
- لا تجعل الزرين داخل عنصر غير قياسي لا يتعرف عليه المحرر.
- لا تجعل الإحصاءات مجرد spans بلا بنية snippet إذا كانت مطلوبة للتحرير.

## بطاقات Solutions & Services

المشكلة القديمة:

- البطاقات كانت تعتمد على `background-image` داخل `<a>`.
- CSS كان يتوقع وجود `<img>` لكن HTML لا يحتوي صورة.
- محرر Odoo لم يستطع التعامل مع الصورة كصورة قابلة للتبديل.

الحل الذي طبقناه:

```xml
<div class="card h-100 alaj-sol-card o_colored_level" data-name="Solution Card">
    <div class="alaj-sol-card-media">
        <img src="/alaj_website/static/src/binary/heroes/hero-overlay.jpg"
             class="img img-fluid o_we_custom_image"
             alt="Chemical and Reagents"
             data-name="Image"/>
    </div>
    <div class="alaj-sol-overlay"></div>
    <div class="card-img-overlay alaj-sol-info d-flex flex-column justify-content-end">
        <h3><a href="/solutions">Chemical and Reagents</a></h3>
        <p>Premium quality chemicals</p>
    </div>
</div>
```

التعليمات:

- استخدم `<img>` حقيقيًا داخل كل بطاقة.
- أضف `o_we_custom_image` على الصورة.
- أضف `data-name="Image"` على الصورة.
- اجعل العنوان داخل `<h3>`.
- اجعل الرابط داخل العنوان أو داخل زر قياسي.
- اجعل الوصف في `<p>`.
- اجعل overlay منفصلًا ولا يمنع الضغط على الصورة.

قاعدة مهمة:

```css
.alaj-sol-overlay {
    pointer-events: none;
}
```

بدون `pointer-events: none` قد تصبح الطبقة الشفافة فوق الصورة مانعة لتحديد الصورة من المحرر.

## بطاقات المنتجات Our Store

القسم يحتوي:

- `Browse`
- `Our Store`
- فلاتر `All`, `Instruments`, `Analyzers`, `Micro Biology`
- زر `Our Store`
- بطاقات منتجات مثل `Balance Scale`, `DR 6000`, `HQ1110`, `LICO 690`
- زر `View` داخل كل بطاقة

ما فعلناه:

- رأس القسم أصبح `s_text_block` للشارة و `s_title` للعنوان.
- الفلاتر أصبحت أزرار `btn`.
- زر `Our Store` أصبح زرًا قياسيًا.
- كل بطاقة منتج أصبحت `s_card card`.
- الصورة داخل كل بطاقة أصبحت `<img>` حقيقيًا قابلًا للتعديل.
- اسم المنتج المختصر والعنوان وزر View أصبحت عناصر منفصلة.

نمط البطاقة:

```xml
<div class="s_card card h-100 alaj-prod-card" data-snippet="s_card" data-name="Product Card">
    <div class="card-body">
        <div class="alaj-prod-card-img">
            <img src="/alaj_website/static/src/binary/products/balance-scale.png"
                 alt="Balance Scale"
                 class="o_we_custom_image"
                 data-name="Image"/>
        </div>
        <p class="alaj-prod-card-code">Balance Scale</p>
        <h3><a href="/shop">Balance Compact Scale 2000G X 1GM</a></h3>
        <a href="/shop" class="btn btn-link alaj-prod-arrow">View</a>
    </div>
</div>
```

التعليمات:

- استخدم `s_card` على كل بطاقة.
- استخدم `card-body` لتجميع محتوى البطاقة.
- استخدم `<img>` قابلًا للتحرير بدل الخلفية.
- اجعل زر View رابطًا بقياسي `btn`.
- لا تجعل البطاقة كلها رابطًا واحدًا إذا كان ذلك يمنع تحرير النصوص الداخلية.

## بطاقات الأخبار News & Events

المشكلة القديمة:

- الصورة الكبيرة كانت ضيقة.
- كانت هناك طبقة شفافة تمنع الوصول للصورة في المحرر.
- بعض الصور لم تكن standard أو لم تكن سهلة الاستبدال.

ما فعلناه:

- كل بطاقة خبر أصبحت تفصل الصورة عن النص.
- الصورة أصبحت `<img class="img img-fluid w-100 h-100 o_we_custom_image" data-name="Image"/>`.
- التاريخ أصبح `s_text_block`.
- عنوان الخبر أصبح `s_title`.
- البطاقة الكبيرة Featured أصبحت تحتوي صورة حقيقية وoverlay لا يمنع الضغط.

نمط بطاقة خبر صغيرة:

```xml
<div class="alaj-news-entry" data-name="News Card">
    <div class="row g-0 alaj-news-card-row">
        <div class="col-md-4 s_col_no_bgcolor">
            <div class="alaj-news-card-img">
                <img src="/alaj_website/static/src/binary/news/event-1.jpg"
                     alt="Event"
                     class="img img-fluid w-100 h-100 o_we_custom_image"
                     data-name="Image"/>
            </div>
        </div>
        <div class="col-md-8 s_col_no_bgcolor">
            <div class="alaj-news-card-body">
                <section class="s_text_block" data-snippet="s_text_block" data-name="News Date">
                    ...
                </section>
                <section class="s_title" data-snippet="s_title" data-name="News Title" data-vcss="001">
                    ...
                </section>
            </div>
        </div>
    </div>
</div>
```

نمط البطاقة الكبيرة:

```xml
<div class="alaj-featured-card o_colored_level" data-name="Featured Article">
    <div class="alaj-featured-card-media">
        <img src="/alaj_website/static/src/binary/news/featured-article.jpg"
             alt="Featured Article"
             class="img img-fluid w-100 h-100 o_we_custom_image"
             data-name="Image"/>
    </div>
    <div class="alaj-featured-overlay"></div>
    <div class="alaj-featured-info">
        ...
    </div>
</div>
```

قاعدة CSS مهمة:

```css
.alaj-featured-overlay {
    pointer-events: none;
}
```

التعليمات:

- لا تجعل overlay يغطي الصورة ويمنع تحديدها.
- لا تستخدم background image للصورة التي يريد المستخدم تعديلها.
- افصل التاريخ والعنوان في snippets داخلية حتى يظهران كحقول قابلة للتحرير.
- اضبط حجم الصورة عبر CSS مثل `object-fit: cover` و `min-height` بدل تصغير الصورة نفسها.

## قسم Founder

النص الكامل الذي ثبتناه:

```text
We continue to transform as we aim for sustainable growth
based on our philosophy.

"Placing our clients at the heart
at everything we do"

Abdullatif H. Abuljadayel
Founder
```

التعليمات:

- إذا كان النص يحتاج تحريرًا مستقلًا، يفضل وضع المقدمة والاقتباس والاسم والدور في عناصر منفصلة.
- الخلفية إن كانت قابلة للتعديل يجب أن تكون صورة حقيقية أو section background يدعمه Odoo.
- إذا استخدمنا overlay فوق الخلفية، يجب ألا يمنع تحديد العناصر.

## الصور

القاعدة:

```xml
<img src="/alaj_website/static/src/binary/..."
     alt="..."
     class="img img-fluid o_we_custom_image"
     data-name="Image"/>
```

التعليمات:

- استخدم `<img>` عندما يكون الهدف أن يغير المستخدم الصورة من المحرر.
- أضف `o_we_custom_image`.
- أضف `data-name="Image"`.
- أضف `alt` واضحًا.
- استخدم `img img-fluid` عند الحاجة لتوافق Bootstrap.
- استخدم `w-100 h-100 object-fit: cover` عبر CSS إذا أردت ملء مساحة البطاقة.
- لا تستخدم `background-image` وحدها لصورة يريد المستخدم تحريرها.

## الطبقات الشفافة Overlays

أي overlay فوق صورة يجب أن يكون آمنًا للمحرر:

```css
.alaj-sol-overlay,
.alaj-featured-overlay,
.alaj-founder-overlay {
    pointer-events: none;
}
```

التعليمات:

- إذا كان overlay يمنع تحديد الصورة أو النص، أضف `pointer-events: none`.
- لا تضع overlay كعنصر يغطي كل البطاقة مع z-index أعلى من النص والصورة بدون سبب.
- اجعل النص فوق الصورة عبر z-index، لكن لا تجعل overlay يلتقط الضغط.

## المنيو standard

المشكلة القديمة:

- المنيو كانت روابط hard-coded داخل `alaj_layout_override.xml`.
- أي تعديل يحتاج تغيير XML وترقية الموديول.
- محرر Odoo لا يستطيع إضافة أو حذف أو إعادة ترتيب روابط hard-coded.

الحل:

- المنيو يجب أن تكون سجلات `website.menu`.
- الهيدر يجب أن يقرأ من `website.menu_id.child_id`.
- الديسكتوب يستخدم `website.navbar_nav` و `website.submenu`.
- الجوال يستخدم نفس بيانات `website.menu` عبر loop أو template recursive.

نمط الديسكتوب:

```xml
<nav class="alaj-nav" t-att-data-content_menu_id="website.menu_id.id" aria-label="Main navigation">
    <t t-call="website.navbar_nav">
        <t t-set="_no_autohide_menu_mobile" t-value="True"/>
        <t t-set="_nav_class" t-valuef="alaj-nav-list"/>
        <t t-foreach="website.menu_id.child_id" t-as="submenu">
            <t t-call="website.submenu">
                <t t-set="item_class" t-valuef="alaj-nav-item nav-item"/>
                <t t-set="link_class" t-valuef="alaj-nav-link nav-link"/>
                <t t-set="dropdown_toggler_classes" t-valuef="d-flex align-items-center gap-2"/>
                <t t-set="dropdown_menu_classes" t-valuef="alaj-nav-dropdown"/>
            </t>
        </t>
    </t>
</nav>
```

نمط الجوال:

```xml
<div class="alaj-drawer-body">
    <t t-foreach="website.menu_id.child_id" t-as="submenu">
        <t t-call="alaj_website.alaj_drawer_menu_item"/>
    </t>
</div>
```

القالب recursive للجوال:

```xml
<template id="alaj_drawer_menu_item" name="ALAJ Drawer Menu Item">
    <t t-if="submenu.is_visible">
        <a t-att-href="submenu._clean_url()" class="alaj-drawer-link" t-att-target="'_blank' if submenu.new_window else None">
            <span t-field="submenu.name"/>
        </a>
        <t t-if="submenu.child_id.filtered(lambda m: m.is_visible)">
            <div class="alaj-drawer-sub">
                <t t-foreach="submenu.child_id" t-as="submenu">
                    <t t-call="alaj_website.alaj_drawer_menu_item"/>
                </t>
            </div>
        </t>
    </t>
</template>
```

التعليمات:

- لا تكتب روابط المنيو يدويًا في الهيدر إذا أردتها قابلة للتحرير.
- اربط المنيو بالـ website عبر `website.menu_id`.
- استخدم `data-content_menu_id` في nav حتى يفهم المحرر ارتباط المنيو.
- استخدم `t-field="submenu.name"` حتى يتعامل Odoo مع الاسم كسجل.
- استخدم `submenu._clean_url()` بدل كتابة URL يدويًا.
- استخدم `submenu.is_visible` لدعم الإخفاء من Odoo.
- إذا احتجت تصميم مخصص، غيّر الكلاسات فقط ولا تكسر مصدر البيانات.

## ملف تجربة المنيو standard

أنشأنا `data/alaj_standard_menu_test.xml` لتجربة منيو مبنية من سجلات `website.menu` مثل `my_web/menus.xml`.

فكرته:

- حذف منيو الموقع الحالية لهذا الموقع.
- إنشاء root menu باسم `std_menu_4`.
- إنشاء عناصر menu كسجلات `website.menu`.
- ربط الموقع بالجذر الجديد عبر:

```xml
<function model="website" name="write">
    <value eval="[ref('alaj_website_record')]"/>
    <value eval="{'menu_id': ref('std_menu_4')}"/>
</function>
```

تعليمات مهمة:

- لا تشغل هذا الملف على الداتابيز إلا عندما تقرر أنت ذلك.
- النصوص العربية التالفة يجب تصحيحها داخل `<field name="name">...</field>` قبل الترقية.
- إذا كانت القائمة مطلوبة لتبقى قابلة للتعديل من الواجهة بعد الترقية، استخدم `noupdate="1"` بحذر حسب سياسة التحديث.

## الهيدر واللوقو

المشكلة:

- عرض صورتين أو صورة غير مقيدة بالحجم تسبب تضخم اللوقو وتشويه الجوال.

الحل:

```xml
<picture>
    <source media="(max-width: 991.98px)" srcset="/alaj_website/static/src/binary/logos/abdullaticophone.png"/>
    <img src="/alaj_website/static/src/binary/logos/AbdullatiCo.png" alt="ALAJ" class="alaj-brand-image"/>
</picture>
```

التعليمات:

- استخدم `picture/source` لاختيار صورة الجوال بدل عرض صورتين.
- صورة الديسكتوب: `static/src/binary/logos/AbdullatiCo.png`.
- صورة الجوال: `static/src/binary/logos/abdullaticophone.png`.
- قيّد الحجم في CSS:

```css
.alaj-brand-image {
    height: 48px;
}
@media (max-width: 991.98px) {
    .alaj-brand-image {
        width: 52px;
        height: auto;
    }
}
```

## صفحات Partners

أضفنا صفحة شركاء في `data/odoo_partners_page.xml`.

فكرتها:

- صفحة `/partners`.
- تقسيم الصور حسب البند.
- روابط من الصفحة الرئيسية تنقل مباشرة للبلوك المناسب.

الأقسام:

- `#trusted-by`
- `#chemicals`
- `#maintenance`
- `#equipment`

روابط البطاقات تحت الهيرو:

- `Chemical & Reagents` إلى `/partners#chemicals`
- `Equipment` إلى `/partners#equipment`
- `Maintenance` إلى `/partners#maintenance`
- `After Sales` إلى `/partners#trusted-by`

تعليمات الصور:

- الصور يجب أن تكون داخل مسار static الخاص بالموديول.
- لا تعتمد على مجلد خارجي غير web-served مثل `my_web/alajlabchem_partners` مباشرة.
- المسار الصحيح يكون مثل:

```text
/alaj_website/static/src/binary/partners_catalog/Chemicals/...
```

## حذف الصفحات والمنيو

عند حذف صفحة مثل `/faq` أو إزالة روابط Projects من الرئيسية، لا يكفي حذف HTML فقط.

التعليمات:

- احذف `website.page` الخاصة بالرابط.
- احذف `website.menu` الخاصة بالرابط.
- احذف أو عطّل view المرتبط إذا كان مسجلًا في الموديول.
- لا تترك رابطًا في الهيدر أو الفوتر أو quick links.

مثال عام:

```xml
<delete model="website.menu" search="[('url', '=', '/faq'), ('website_id', '=', ref('alaj_website_record'))]"/>
<delete model="website.page" search="[('url', '=', '/faq'), ('website_id', '=', ref('alaj_website_record'))]"/>
```

## CSS والاستايل

الهدف ليس إزالة الاستايل الخاص. الهدف أن يبقى الشكل كما هو مع بناء قابل للتحرير.

التعليمات:

- اترك الكلاسات المخصصة مثل `alaj-news-card`, `alaj-sol-card`, `alaj-prod-card`.
- اجعل هذه الكلاسات تضبط الشكل فقط.
- لا تجعل CSS هو مصدر المحتوى.
- لا تستخدم CSS لإضافة نصوص مهمة عبر `content`.
- لا تجعل CSS يخفي عناصر editor القياسية.
- لا تجعل z-index أو overlay يمنع تحديد الصورة أو النص.

## أخطاء واجهناها وكيف نتجنبها

رسالة `This block is outdated`:

- سببها غالبًا `data-snippet` لا يطابق template مسجل.
- أو snippet ليس مسجلًا في لوحة snippets.
- أو version attrs غير موجودة أو غير متوافقة.
- أو القالب تغير ولم يحدث snippet definition.

حلها:

- تأكد من وجود `<template id="s_alaj_x">`.
- تأكد من أن section يحمل `data-snippet="s_alaj_x"`.
- تأكد من تسجيله داخل `alaj_website_snippets`.
- استخدم `data-vxml="001"` و `data-vcss="001"`.

خطأ `xpath cannot be located`:

- سببه أن xpath يستهدف عنصرًا غير موجود في القالب الأب.
- حدث ذلك عندما حاولنا استهداف بنية social/share غير مطابقة.

حلها:

- افحص القالب الأب قبل كتابة xpath.
- استخدم xpath أكثر مرونة عند الحاجة مثل `contains(@href, 'twitter.com/intent/tweet')`.
- لا تفترض وجود كلاس إذا لم يكن موجودًا فعلًا في قالب Odoo.

خطأ `&` في XML:

- في XML يجب كتابة `&amp;` بدل `&`.
- هذا صحيح وليس خطأ عرض.

مثال:

```xml
Equipment &amp; Instruments
```

يعرض في المتصفح:

```text
Equipment & Instruments
```

مشكلة الصورة لا تقبل التحرير:

- السبب عادة أن الصورة ليست `<img>`.
- أو overlay فوقها يمنع الضغط.
- أو الصورة داخل background فقط.

الحل:

- استخدم `<img class="o_we_custom_image" data-name="Image"/>`.
- أضف `pointer-events: none` للـ overlay.

مشكلة 404 للـ thumbnail:

- سببها مسار thumbnail غير موجود.
- استبدلنا `s_cards_grid.svg` بمسار موجود مثل `s_three_columns.svg`.

## قواعد XML

التعليمات:

- استخدم `&amp;` بدل `&`.
- لا تكتب attribute غير مغلق.
- لا تستخدم `>` أو `<` داخل النصوص بدون تهريب.
- افحص الملف قبل الترقية.

أمر تحقق محلي بدون رفع على الداتابيز:

```powershell
[xml](Get-Content -Raw -LiteralPath "my_web\alaj_website\data\odoo_homepage.xml") | Out-Null
```

## سير العمل الصحيح لأي تعديل جديد

1. حدد هل العنصر صفحة، بلوك، بطاقة، صورة، زر، أو منيو.
2. إذا كان صفحة، ضعها داخل `website.layout` و `#wrap`.
3. إذا كان بلوكًا رئيسيًا، أنشئ `template id` له في `alaj_homepage_snippets.xml`.
4. أضف `data-snippet`, `data-vxml`, `data-vcss` على section.
5. سجل snippet في لوحة snippets.
6. ابن النصوص الداخلية بـ `s_text_block` أو `s_title`.
7. ابن البطاقات بـ `s_card card` أو بنية card مفهومة.
8. استخدم `<img>` حقيقيًا لكل صورة قابلة للتحرير.
9. تأكد أن أي overlay لا يمنع التحديد.
10. اربط الروابط والأزرار بـ `btn` أو روابط Odoo واضحة.
11. تحقق من XML محليًا.
12. لا ترفع على الداتابيز إلا بعد قرار صريح.

## قائمة تدقيق لأي بلوك جديد

- هل للبلوك `data-snippet`؟
- هل يوجد template بنفس id؟
- هل snippet مسجل في `alaj_website_snippets`؟
- هل يوجد `data-vxml` و `data-vcss`؟
- هل النصوص المهمة داخل `s_text_block` أو `s_title`؟
- هل الأزرار روابط `btn`؟
- هل الصور المهمة `<img>` وليست background فقط؟
- هل الصور تحمل `o_we_custom_image` و `data-name="Image"`؟
- هل overlay لا يمنع الضغط؟
- هل البطاقات تستخدم `card` أو `s_card`؟
- هل الروابط لا تكسر تجربة التحرير؟
- هل thumbnail موجود؟
- هل XML صالح؟

## قائمة تدقيق للمنيو

- هل المنيو سجلات `website.menu`؟
- هل الموقع مربوط بالجذر الصحيح عبر `website.menu_id`؟
- هل الهيدر يقرأ من `website.menu_id.child_id`؟
- هل الديسكتوب يستخدم `website.navbar_nav` و `website.submenu`؟
- هل الجوال يستخدم نفس مصدر البيانات؟
- هل لا توجد روابط hard-coded مكررة في الهيدر؟
- هل الفوتر لا يحتوي روابط محذوفة مثل `/faq` إذا أزلت الصفحة؟
- هل الترتيب يتم عبر `sequence` أو Website Editor؟

## متى نستخدم hard-coded HTML

يمكن استخدام HTML مخصص فقط عندما يكون الغرض زخرفيًا أو بنيويًا، وليس عنصرًا يحتاج تعديلًا من المحرر.

مسموح:

- wrappers للتصميم.
- divs للـ grid.
- overlay بصري.
- icons زخرفية.
- CSS classes.

غير مناسب:

- نصوص يريد المستخدم تعديلها.
- صور يريد المستخدم استبدالها.
- أزرار يريد المستخدم تعديل رابطها.
- منيو يريد المستخدم إدارتها.
- بطاقات يراد تكرارها وحذفها بسهولة.

## الخلاصة العملية

ما جعل الصفحة الرئيسية standard ليس إزالة التصميم الخاص، بل فصل التصميم عن قابلية التحرير:

- التصميم بقي عبر كلاسات `alaj-*`.
- البلوكات أصبحت snippets مسجلة.
- النصوص أصبحت داخل snippets قياسية.
- الصور أصبحت `<img>` قابلة للتحديد.
- البطاقات أصبحت `card` و `s_card`.
- المنيو أصبحت مبنية من `website.menu`.
- overlay أصبح لا يمنع المحرر.
- XML صار يحترم قواعد Odoo و QWeb.

هذه هي القواعد التي يجب اتباعها في أي صفحة جديدة داخل هذا الموقع.
