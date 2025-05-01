
---
title: "How I migrated a WordPress Site to a new hosting plan and avoided a huge renewal price"
description: "Learn how to migrate a WordPress site between hosting plans, avoid high renewal fees, and troubleshoot issues like .htaccess prepends causing 500 errors."
date: 2025-05-01T23:00:00+04:00
tags:
  - wordpress
  - hosting
  - migration
  - blog
categories:
  - reflections
draft: false
comments: false
hidden: false
---

## Context: The migration decision

When faced with the high renewal cost of a hosting plan, I had to make a choice: either pay up for another year or move the site to a more affordable plan. Since the site is more of a branding tool for its writer than a revenue-generating machine, the budget-friendly option was the clear winner. After all, this wasn’t about profitability — it was about maintaining the site without the burden of high renewal costs. 

Given that my involvement in this project is pro bono, I’ve been looking for ways to keep costs low until the ultimate goal is realized: **migrating everything to Hugo**. This will take careful planning since there are **over 3,000 articles** to account for. But for now, the priority was to avoid paying a steep renewal price while maintaining control over the site.

So, I opted to move the WordPress site to a more affordable hosting plan. It wasn’t as simple as I expected, but the goal was clear: move the site, **save on renewal fees**, and hopefully learn something along the way. 🚀

---

## Step 1: Backup, the easy way

The first step in the migration was backing up the entire WordPress site — including both the files and the database. For this, I took advantage of the easy routes provided by Hostinger, which offered a simple way to create a tar.gz file for the entire site and a database backup through their hPanel UI. These steps were straightforward and saved a lot of time in setting up the backups, I could prepare myself a coffee in the meantime. ☕

## Step 2: Upload challenges

The real challenge came when I tried to upload the tarball I just downloaded. The 30GB file was far too large for a single upload through the web client: with an estimated transfer time of 8 hours — a duration that was way too long — it was just too slow and, frankly, risky to rely on this method.  
Especially with a 1MB/s connection, which was likely throttled by the web interface. 😤

That’s when I checked if SFTP and SSH access were available on my plan. This turned out to be a game-changer: it gave me much more control over the transfer process. And I was able to upload files at twice the speed of the web client. ⚡

With SSH access in hand, I made sure the `cat` and `tar` commands were available on the server (you're never too sure). Then after splitting my archive into multiple files, I was able to upload the parts using SFTP and, once all the parts were on the server, I reassembled the files into the original tar.gz archive before unpacking them. This method provided more stability and speed than relying on the web interface.

Steps summary for file upload:

1. Split the archive on local.

```bash
split -b 1G large_backup.tar.gz large_backup_part_
```

2. Upload the parts to the new host using sftp.

3. **SSH** into the new host.

4. Reassemble the parts.

```bash
cat large_backup_part_* > large_backup.tar.gz
```

5. Extract the files.

```bash
tar -xzvf large_backup.tar.gz -C public_html
```

6. Finally, move the extracted files where needed.

**Side note**: At this point, I was starting to question my decision. Surely the time I was spending here could have been spent elsewhere, maybe paying the renewal and keeping things simple. 🤔

## Step 3: The migration or how I broke everything

Once the files were uploaded to the new plan, the next step was the database migration. Importing the database via **phpMyAdmin** was super smooth, I just removed all the existing tables before the import and voila. ✔️

Now let's configure WordPress to use this db by editing `wp-config.php`.

1. I updated the database details in `wp-config.php`:

```php
define('DB_NAME', 'new_database_name');
define('DB_USER', 'new_database_user');
define('DB_PASSWORD', 'new_database_password');
define('DB_HOST', 'new_database_host');
```

2. Update site URLs in the database `wp_options` table to point to the new domain:

```sql
UPDATE wp_options SET option_value = 'http://yournewdomain.com' WHERE option_name = 'siteurl' OR option_name = 'home';
```

## Step 4: The `.htaccess` issue - A lesson in troubleshooting

So far, so good. But when I went to test the site, I encountered an issue.

Here’s where things got fun. After migrating the site and testing, I was faced with 500 internal server error.  
Turns out, my trusty `.htaccess` file had some prepending rules; thanks to the Wordfence plugin that caused conflicts with the WordPress redirects.

Turns out that the plugin was prepending the wrong path, leading to broken redirects. A little digging in the `.htaccess` file revealed the issue.

Here’s a anonymized version of the problematic section:

```apache
# Wordfence WAF
<IfModule LiteSpeed>
php_value auto_prepend_file '/home/user-identifier/domains/politicothon.com/public_html/wordfence-waf.php'
</IfModule>
<IfModule lsapi_module>
php_value auto_prepend_file '/home/user-identifier/domains/politicothon.com/public_html/wordfence-waf.php'
</IfModule>

# END Wordfence WAF
```

**The Fix:** I just commented out the faulty lines, ensuring that the redirects were working properly, and retriggered the htaccess modifications from the plugin. It finally worked. 🎉

## Step 5: Moving emails and other unexpected tasks

Of course, no migration is ever just about the site files. I also had to move the emails. Thankfully, Hostinger’s made this relatively painless.

## Final testing and adjustments

By now, everything was uploaded, configured, and most issues were resolved. But like any migration, I did some final checks:

1. **Flush Permalinks** in WordPress: This ensured no broken links.
2. **Test the site**: I manually visited the site to verify latest pages, posts, and media were intact.
3. **Verify emails**: I sent and received a few test emails to confirm success. ✅

---

## Conclusion: is the time spent worth it?

Looking back, the migration was a learning experience. Sure, I saved on the renewal cost, but the time spent troubleshooting, moving files, dealing with `.htaccess` issues, and moving emails probably cost me more in time than the actual price of the renewal.

But, in the end, I learned a bit about WordPress, hosting configurations, and server-side issues. If anything, it prepared me for future migrations (though next time, I might be more inclined to just pay the renewal and avoid the hassle).

### Next Up: Moving a WordPress Site to Hugo

If WordPress to WordPress migration was ok, next time, I’m going to take on the challenge of moving from WordPress to Hugo. It's bound to be a much more challenging adventure — and this time, I might even save more time (or spend less in the process!).

---

**Lesson learned**: Sometimes, the **convenience of not moving** comes at the cost of the **time spent** in the process. But hey, time is money, right? 😜


### Fun Quote:
_"It felt like I was spending more time doing this than if I just paid for the renewal — but I was learning, right?"_
