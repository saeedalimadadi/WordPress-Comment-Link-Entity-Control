# WordPress Comment Link & Entity Control

This code snippet provides a dual solution for managing the content of your WordPress comments. It addresses two common concerns:

1.  **Converts HTML Entities:** Ensures that any HTML entities (like `&lt;` for `<` or `&amp;` for `&`) in comment text are properly converted. This is a good practice for display consistency and preventing unexpected rendering issues.
2.  **Disables Links within Comments:** Strips out all HTML tags from comment content, effectively removing any hyperlinks that commenters might try to include. This is a common strategy to reduce spam and ensure comments focus on the discussion rather than self-promotion or malicious links.

## Why use this code?

* **Enhanced Security:** By stripping all HTML tags, you drastically reduce the risk of malicious code (like XSS attacks) being injected through comments.
* **Spam Reduction:** Prevents spammers from inserting backlinks or promotional links within the comment body, a frequent tactic for unsolicited content.
* **Cleaner Comments:** Ensures comment content is plain text, leading to a consistent and readable appearance across your site.
* **Improved Focus:** Encourages commenters to engage in discussion rather than using the comment section for self-promotion.

## Installation

There are two primary methods to implement this code:

### Method 1: As a Standalone Plugin (Recommended)

Creating a small, dedicated plugin is the most robust way to add this customization. It ensures your modification remains active regardless of theme changes.

1.  Create a new folder named `wp-comment-cleanup` inside your WordPress site's `wp-content/plugins/` directory.
2.  Inside this new folder, create a file named `wp-comment-cleanup.php`.
3.  Copy and paste the following code into `wp-comment-cleanup.php`:

    ```php
    <?php
    /**
     * Plugin Name: WordPress Comment Link & Entity Control
     * Description: Converts HTML entities and removes all links/HTML tags from WordPress comments for enhanced security and spam control.
     * Version: 1.0
     * Author: Your Name (Optional)
     * License: MIT
     */

    /**
     * Converts HTML special characters in comment text.
     * Note: make_clickable is intentionally included for potential future use or if you want to make URLs clickable after entities are handled,
     * but it will be overridden by disable_comment_links if that filter runs afterwards.
     * The effective result of this snippet is that links are removed.
     *
     * @param string $comment_text The comment content.
     * @return string Modified comment content.
     */
    function convert_comment_html_entities($comment_text) {
        $comment_text = htmlspecialchars($comment_text);
        // make_clickable will convert text URLs into clickable links.
        // However, the 'disable_comment_links' filter, running later, will strip all HTML,
        // effectively removing these links.
        $comment_text = make_clickable($comment_text);
        return $comment_text;
    }

    /**
     * Removes all HTML tags, including links, from comment text.
     * This filter runs after 'convert_comment_html_entities' due to its higher priority (20 vs 10).
     *
     * @param string $comment_text The comment content.
     * @return string Modified comment content.
     */
    function disable_comment_links($comment_text) {
        $comment_text = strip_tags($comment_text); // This removes all HTML tags, including <a> tags
        return $comment_text;
    }

    // Filters are applied in order of their priority.
    // convert_comment_html_entities runs first (priority 10).
    add_filter('comment_text', 'convert_comment_html_entities', 10, 1);
    // disable_comment_links runs second (priority 20), effectively stripping any HTML,
    // including links potentially made clickable by the first filter.
    add_filter('comment_text', 'disable_comment_links', 20, 1);
    ?>
    ```

4.  Go to your WordPress admin dashboard, navigate to **"Plugins"**, and **activate** the "WordPress Comment Link & Entity Control" plugin.

### Method 2: Adding to your Theme's functions.php File

You can add this code directly to your active theme's `functions.php` file. **Before doing so, it's highly recommended to back up your `functions.php` file.**

1.  Navigate to `wp-content/themes/YourThemeName/` (replace `YourThemeName` with the actual name of your active theme).
2.  Open the `functions.php` file.
3.  Add the following code to the end of the file (before the closing `?>` tag, if one exists):

    ```php
    /**
     * Converts HTML special characters in comment text.
     * Note: make_clickable is intentionally included for potential future use or if you want to make URLs clickable after entities are handled,
     * but it will be overridden by disable_comment_links if that filter runs afterwards.
     * The effective result of this snippet is that links are removed.
     *
     * @param string $comment_text The comment content.
     * @return string Modified comment content.
     */
    function convert_comment_html_entities($comment_text) {
        $comment_text = htmlspecialchars($comment_text);
        // make_clickable will convert text URLs into clickable links.
        // However, the 'disable_comment_links' filter, running later, will strip all HTML,
        // effectively removing these links.
        $comment_text = make_clickable($comment_text);
        return $comment_text;
    }

    /**
     * Removes all HTML tags, including links, from comment text.
     * This filter runs after 'convert_comment_html_entities' due to its higher priority (20 vs 10).
     *
     * @param string $comment_text The comment content.
     * @return string Modified comment content.
     */
    function disable_comment_links($comment_text) {
        $comment_text = strip_tags($comment_text); // This removes all HTML tags, including <a> tags
        return $comment_text;
    }

    // Filters are applied in order of their priority.
    // convert_comment_html_entities runs first (priority 10).
    add_filter('comment_text', 'convert_comment_html_entities', 10, 1);
    // disable_comment_links runs second (priority 20), effectively stripping any HTML,
    // including links potentially made clickable by the first filter.
    add_filter('comment_text', 'disable_comment_links', 20, 1);
    ```

## Important Note on `make_clickable` and `strip_tags`

In this specific code, `make_clickable` converts plain text URLs into HTML links (e.g., `<a href="...">`). However, because `disable_comment_links` runs *after* `convert_comment_html_entities` (due to its higher priority of 20 vs. 10), `strip_tags` will then remove *all* HTML tags, including the ones `make_clickable` just created.

**The net effect of this combined code is that all HTML, including links, will be stripped from comments.** If your intention was to allow *some* HTML or clickable links while still converting entities, you would need to adjust the filters or their priorities. As written, this snippet prioritizes security and cleanliness by removing all HTML.

## Contributing

Contributions are welcome! If you have suggestions or improvements for this code, feel free to open a "Pull Request" or report an "Issue."

## License

This project is licensed under the MIT License. For more details, see the `LICENSE` file.

# کنترل لینک‌ها و موجودیت‌های HTML در دیدگاه‌های وردپرس

این قطعه کد یک راه‌حل دوگانه برای مدیریت محتوای دیدگاه‌های وردپرس شما ارائه می‌دهد. این کد به دو نگرانی رایج می‌پردازد:

1.  **تبدیل موجودیت‌های HTML:** اطمینان حاصل می‌کند که هرگونه موجودیت HTML (مانند `&lt;` برای `<` یا `&amp;` برای `&`) در متن دیدگاه‌ها به درستی تبدیل شوند. این یک روش خوب برای سازگاری نمایش و جلوگیری از مشکلات غیرمنتظره در رندر شدن است.
2.  **غیرفعال کردن لینک‌ها در داخل دیدگاه‌ها:** تمام تگ‌های HTML را از محتوای دیدگاه حذف می‌کند و به طور موثر هرگونه لینک (هایپرلینک) را که نظردهندگان ممکن است بخواهند اضافه کنند، از بین می‌برد. این یک استراتژی رایج برای کاهش هرزنامه (اسپم) و اطمینان از تمرکز دیدگاه‌ها بر بحث به جای تبلیغ شخصی یا لینک‌های مخرب است.

## چرا از این کد استفاده کنیم؟

* **افزایش امنیت:** با حذف تمام تگ‌های HTML، خطر تزریق کدهای مخرب (مانند حملات XSS) از طریق دیدگاه‌ها به شدت کاهش می‌یابد.
* **کاهش هرزنامه:** از قرار دادن بک‌لینک‌ها یا لینک‌های تبلیغاتی توسط اسپمرها در بدنه دیدگاه جلوگیری می‌کند، که یک تاکتیک رایج برای محتوای ناخواسته است.
* **دیدگاه‌های تمیزتر:** اطمینان حاصل می‌کند که محتوای دیدگاه‌ها متن ساده است، که منجر به ظاهری یکنواخت و خوانا در سراسر سایت شما می‌شود.
* **تمرکز بهبود یافته:** نظردهندگان را تشویق می‌کند تا به جای استفاده از بخش دیدگاه برای تبلیغ شخصی، در بحث مشارکت کنند.

## نصب

برای پیاده‌سازی این کد دو روش اصلی وجود دارد:

### روش ۱: به عنوان یک افزونه مستقل (توصیه شده)

ایجاد یک افزونه کوچک و اختصاصی، قوی‌ترین راه برای افزودن این سفارشی‌سازی است. این تضمین می‌کند که تغییر شما صرف‌نظر از تغییر قالب، فعال باقی بماند.

1.  یک پوشه جدید با نام `wp-comment-cleanup` در مسیر `wp-content/plugins/` سایت وردپرسی خود ایجاد کنید.
2.  در داخل این پوشه جدید، یک فایل با نام `wp-comment-cleanup.php` ایجاد کنید.
3.  کد زیر را در `wp-comment-cleanup.php` کپی و جایگذاری کنید:

    ```php
    <?php
    /**
     * Plugin Name: WordPress Comment Link & Entity Control
     * Description: Converts HTML entities and removes all links/HTML tags from WordPress comments for enhanced security and spam control.
     * Version: 1.0
     * Author: Your Name (Optional)
     * License: MIT
     */

    /**
     * Converts HTML special characters in comment text.
     * Note: make_clickable is intentionally included for potential future use or if you want to make URLs clickable after entities are handled,
     * but it will be overridden by disable_comment_links if that filter runs afterwards.
     * The effective result of this snippet is that links are removed.
     *
     * @param string $comment_text The comment content.
     * @return string Modified comment content.
     */
    function convert_comment_html_entities($comment_text) {
        $comment_text = htmlspecialchars($comment_text);
        // make_clickable will convert text URLs into clickable links.
        // However, the 'disable_comment_links' filter, running later, will strip all HTML,
        // effectively removing these links.
        $comment_text = make_clickable($comment_text);
        return $comment_text;
    }

    /**
     * Removes all HTML tags, including links, from comment text.
     * This filter runs after 'convert_comment_html_entities' due to its higher priority (20 vs 10).
     *
     * @param string $comment_text The comment content.
     * @return string Modified comment content.
     */
    function disable_comment_links($comment_text) {
        $comment_text = strip_tags($comment_text); // This removes all HTML tags, including <a> tags
        return $comment_text;
    }

    // Filters are applied in order of their priority.
    // convert_comment_html_entities runs first (priority 10).
    add_filter('comment_text', 'convert_comment_html_entities', 10, 1);
    // disable_comment_links runs second (priority 20), effectively stripping any HTML,
    // including links potentially made clickable by the first filter.
    add_filter('comment_text', 'disable_comment_links', 20, 1);
    ?>
    ```

4.  وارد پنل مدیریت وردپرس خود شوید، به بخش **"افزونه‌ها"** بروید و افزونه **"کنترل لینک‌ها و موجودیت‌های HTML در دیدگاه‌های وردپرس"** را **فعال کنید**.

### روش ۲: اضافه کردن به فایل functions.php قالب شما

می‌توانید این کد را مستقیماً به فایل `functions.php` قالب فعال خود اضافه کنید. **پیشنهاد اکید می‌شود قبل از انجام این کار، از فایل `functions.php` خود یک پشتیبان (backup) تهیه کنید.**

1.  به مسیر `wp-content/themes/YourThemeName/` بروید (به جای `YourThemeName` نام واقعی قالب فعال خود را قرار دهید).
2.  فایل `functions.php` را باز کنید.
3.  کد زیر را به انتهای فایل (قبل از تگ بستن `?>`، در صورت وجود) اضافه کنید:

    ```php
    /**
     * Converts HTML special characters in comment text.
     * Note: make_clickable is intentionally included for potential future use or if you want to make URLs clickable after entities are handled,
     * but it will be overridden by disable_comment_links if that filter runs afterwards.
     * The effective result of this snippet is that links are removed.
     *
     * @param string $comment_text The comment content.
     * @return string Modified comment content.
     */
    function convert_comment_html_entities($comment_text) {
        $comment_text = htmlspecialchars($comment_text);
        // make_clickable will convert text URLs into clickable links.
        // However, the 'disable_comment_links' filter, running later, will strip all HTML,
        // effectively removing these links.
        $comment_text = make_clickable($comment_text);
        return $comment_text;
    }

    /**
     * Removes all HTML tags, including links, from comment text.
     * This filter runs after 'convert_comment_html_entities' due to its higher priority (20 vs 10).
     *
     * @param string $comment_text The comment content.
     * @return string Modified comment content.
     */
    function disable_comment_links($comment_text) {
        $comment_text = strip_tags($comment_text); // This removes all HTML tags, including <a> tags
        return $comment_text;
    }

    // Filters are applied in order of their priority.
    // convert_comment_html_entities runs first (priority 10).
    add_filter('comment_text', 'convert_comment_html_entities', 10, 1);
    // disable_comment_links runs second (priority 20), effectively stripping any HTML,
    // including links potentially made clickable by the first filter.
    add_filter('comment_text', 'disable_comment_links', 20, 1);
    ```

## نکته مهم در مورد `make_clickable` و `strip_tags`

در این کد خاص، `make_clickable` آدرس‌های متنی ساده را به لینک‌های HTML تبدیل می‌کند (مثلاً `<a href="...">`). با این حال، از آنجایی که `disable_comment_links` *بعد از* `convert_comment_html_entities` (به دلیل اولویت بالاتر ۲۰ در مقابل ۱۰) اجرا می‌شود، `strip_tags` سپس *تمام* تگ‌های HTML، از جمله تگ‌هایی که `make_clickable` ایجاد کرده است، را حذف خواهد کرد.

**نتیجه خالص این کد ترکیبی این است که تمام HTML، از جمله لینک‌ها، از دیدگاه‌ها حذف خواهد شد.** اگر قصد شما این بود که *برخی* HTML یا لینک‌های قابل کلیک را مجاز کنید در حالی که هنوز موجودیت‌ها را تبدیل می‌کنید، باید فیلترها یا اولویت‌های آنها را تنظیم کنید. همانطور که نوشته شده، این قطعه کد با حذف تمام HTML، امنیت و تمیزی را در اولویت قرار می‌دهد.

## مشارکت (Contributing)

مشارکت شما خوشایند است! اگر پیشنهاد یا بهبودهایی برای این کد دارید، می‌توانید یک "Pull Request" ایجاد کنید یا "Issue" جدیدی را گزارش دهید.

## مجوز (License)

این پروژه تحت مجوز MIT منتشر شده است. برای جزئیات بیشتر به فایل `LICENSE` مراجعه کنید.
