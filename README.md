# RandomThoughtsInTraffic.com

Where real traffic meets network traffic.

This repository is the codebase for a production WordPress site focused on technical writing about web fundamentals (HTML, CSS, JavaScript) with a human voice.

## Live site

- https://randomthoughtsintraffic.com

## What’s in this repo (important)

This is a full WordPress installation checked into source control:

- WordPress core: `wp-admin/`, `wp-includes/`, plus root PHP entrypoints
- “App code”: `wp-content/` (themes, plugins, uploads, caches)
- Local/dev database snapshot: [u942215055_BodOV.sql](u942215055_BodOV.sql)

If you’re used to WordPress repos that track only `wp-content/`, note that core is included here and is treated as part of the workspace.

## Tech stack

- CMS: WordPress
- Runtime: PHP
- Database: MariaDB or MySQL
- Web server (local): Apache via XAMPP (recommended for Windows)

### Themes

- Primary theme: Ashe — [wp-content/themes/ashe/](wp-content/themes/ashe/)
- Other bundled themes (WordPress defaults): [wp-content/themes/](wp-content/themes/)

### Plugins

The `wp-content/plugins/` folder contains (at minimum):

- LiteSpeed Cache: caching/performance
- Contact Form 7: forms
- Google Site Kit: analytics/search-console integration
- Insert Headers and Footers: header/footer scripts
- Classic Widgets: legacy widgets UI
- Hostinger plugins (site tooling/onboarding)

## Local development (Windows + XAMPP)

### Prerequisites

- XAMPP installed (Apache + MariaDB)
- PHP compatible with the installed WordPress + plugins
  - The included database dump was generated with PHP 7.2 + MariaDB (see the header in [u942215055_BodOV.sql](u942215055_BodOV.sql)).
  - Some bundled plugins may require newer PHP versions; if you see fatal errors, try a newer XAMPP/PHP.

### 1) Put the site in XAMPP’s web root

Place this folder under XAMPP’s document root, typically:

- `C:\xampp\htdocs\039-2026-Random-Thoughts-in-Traffic`

Then the site URL will be:

- `http://localhost/039-2026-Random-Thoughts-in-Traffic/`

### 2) Start services

Start **Apache** and **MySQL/MariaDB** from the XAMPP Control Panel.

If you’re using the VS Code workspace that ships with helper tasks, you may also see tasks like “XAMPP: Start” and “XAMPP: Stop” available in the command palette.

### 3) Create a local database

Using phpMyAdmin (usually `http://localhost/phpmyadmin`):

1. Create a database (any name is fine).
2. Ensure it uses `utf8mb4` where possible.

### 4) Import the DB dump

Import [u942215055_BodOV.sql](u942215055_BodOV.sql) into your local database.

Notes:

- The dump includes tables prefixed with `wp_`.
- The dump contains production URLs (e.g. `https://randomthoughtsintraffic.com`) which you’ll want to rewrite for local.

### 5) Configure WordPress database settings

Update [wp-config.php](wp-config.php) with your local DB credentials:

- `DB_NAME`
- `DB_USER`
- `DB_PASSWORD`
- `DB_HOST`

If you prefer, you can start from [wp-config-sample.php](wp-config-sample.php) and create a fresh `wp-config.php`.

### 6) Fix site URL after import (required)

After importing the DB, update `home` and `siteurl` so WordPress generates correct links locally.

In phpMyAdmin → SQL (replace the URL with your local URL):

```sql
UPDATE wp_options
SET option_value = 'http://localhost/039-2026-Random-Thoughts-in-Traffic'
WHERE option_name IN ('home', 'siteurl');
```

If permalinks or content still link to production, you may also need a broader search/replace across post content and meta. Prefer WP-CLI’s `search-replace` if you have it; otherwise use a safe migration plugin and avoid changing the `guid` column unless you know why.

### 7) Log in

Visit:

- `http://localhost/039-2026-Random-Thoughts-in-Traffic/wp-admin/`

If you don’t know the admin password from the imported DB, reset it directly:

Option A (fast/dev-only): set a known password hash with MD5

```sql
UPDATE wp_users
SET user_pass = MD5('change-me-now')
WHERE user_login = 'admin';
```

Option B: in the WP admin UI, use “Lost your password?” (requires local email deliverability), or use WP-CLI if installed.

## Developer workflow

### Where to make changes

- Theme work (templates/styles): [wp-content/themes/ashe/](wp-content/themes/ashe/)
  - If you’re doing significant changes, consider introducing a child theme rather than editing the vendor theme directly.
- Plugins: [wp-content/plugins/](wp-content/plugins/)
  - Prefer configuration over code edits; treat plugin source as vendor.
- Uploads/media: [wp-content/uploads/](wp-content/uploads/)

### WordPress core

Core is present in this repo (`wp-admin/`, `wp-includes/`). In most workflows you should avoid editing core; do upgrades using WordPress update flows (or by replacing core files) and track changes carefully.

## Debugging

### Enable WP debugging

In [wp-config.php](wp-config.php), set:

```php
define('WP_DEBUG', true);
define('WP_DEBUG_LOG', true);
define('WP_DEBUG_DISPLAY', false);
```

Then check `wp-content/debug.log`.

### Common local issues

- “Looks like you already installed WordPress” but wrong URL: update `home`/`siteurl` in `wp_options` (see above).
- White screen / fatal error: check PHP version and `wp-content/debug.log`.
- Stale assets / odd caching behavior: temporarily disable LiteSpeed Cache for local, and clear `wp-content/cache/` if present.

## Deployment & migration notes

This repo reflects a real site. Typical safe deployment for WordPress involves:

- Back up files + DB before changes
- Update `wp-config.php` on the server (never reuse local credentials)
- Migrate DB with proper URL rewriting (`home`, `siteurl`, and content)
- Keep `wp-content/uploads/` in sync (media)
- Validate permalinks, forms, and caching after deploy

If you use a hosting control panel (e.g., Hostinger), their WordPress tooling may manage some of these steps for you.

## Security / secrets (read this)

- Treat [wp-config.php](wp-config.php) as sensitive: it contains DB credentials and authentication keys.
- Do not commit real secrets to public repos.
- If secrets have ever been exposed, rotate them:
  - Change DB password(s)
  - Regenerate WordPress salts/keys

## Content index

Featured posts are indexed in [llms.txt](llms.txt).

## License & attribution

- WordPress: GPL
- Ashe theme: GPLv3 or later (see [wp-content/themes/ashe/readme.txt](wp-content/themes/ashe/readme.txt))
- Third-party plugins: under their respective licenses
