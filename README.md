# Civic Advocacy Static Site

This repository contains the source for a lightweight static website intended for civic or political advocacy campaigns. The HTML, CSS, images, and supporting assets are designed to be portable so they can be hosted directly on GitHub Pages, served from a traditional static web host, or repurposed as the foundation for a WordPress theme.

## Repository layout

```
/
├── README.md                # Project documentation and contributor guidance
├── test                     # Placeholder file used by automated repository checks
└── docs/                    # (Create on demand) Static site source used for GitHub Pages deployments
    ├── assets/
    │   ├── css/             # Compiled stylesheets (e.g., main.css)
    │   ├── js/              # Vanilla JS or transpiled bundles
    │   └── img/             # Optimized imagery (WebP/AVIF preferred with PNG/JPEG fallbacks)
    ├── index.html           # Landing page matching the campaign homepage
    ├── policy.html          # Example policy detail page
    └── shared/              # Includes such as header.html, footer.html for reuse
```

> **Note:** The `docs/` directory is the conventional GitHub Pages publishing root. If you are working locally you can keep sources elsewhere (for example `src/`) and copy or build into `docs/` before deployment.

## Viewing the static site

### Local development

1. Ensure your static assets (HTML, CSS, JS, images) live under `docs/`.
2. From the repository root, start a simple HTTP server:
   ```bash
   cd docs
   python3 -m http.server 8000
   ```
3. Visit [http://localhost:8000](http://localhost:8000) to preview the site. Update files and refresh the browser to see changes.
4. When finished, stop the server with `Ctrl + C`.

Any other static server (e.g., `npx serve docs`, `ruby -run -ehttpd docs -p 8000`) works equally well; the key is that assets are served relative to the `docs/` directory root.

### GitHub Pages

1. Push the repository to GitHub and open the repository settings.
2. Under **Pages**, set the source to `Deploy from a branch`, select the `main` (or default) branch, and choose `/docs` as the publishing folder.
3. Save the configuration and wait for GitHub Pages to build the site. The published URL will be shown in the Pages settings panel.
4. For custom domains, add the domain under Pages settings and create the required DNS `CNAME` record pointing to `<username>.github.io`.

## Converting to a WordPress theme

To migrate the static markup into a WordPress theme:

- Create a new theme directory in `wp-content/themes/civic-advocacy/` and copy the assets directory (`assets/`) directly.
- Break the static HTML into reusable PHP templates:
  - `front-page.php`: Paste the contents of `docs/index.html`, replacing hard-coded assets with `get_template_directory_uri()` references.
  - `page-policy.php`: Convert `docs/policy.html` or other issue pages into a dedicated page template. Register it with a `Template Name` header so editors can assign it to Pages.
  - `header.php` and `footer.php`: Move the `<head>` and closing markup from the shared includes into `header.php` and `footer.php`. Enqueue styles/scripts in `functions.php` using `wp_enqueue_style`/`wp_enqueue_script` rather than hard-coded tags.
  - `inc/header.php`, `inc/footer.php`, `template-parts/hero.php`, etc., can hold modular sections that you `get_template_part()` from the main templates for reusability.
- Add a `functions.php` file that enqueues assets, registers navigation menus (`register_nav_menus` for primary/secondary nav), defines custom logo support, and optionally registers widget areas for footers or sidebars.
- Use `style.css` with the theme header comment (Theme Name, Author, Version, etc.) so WordPress recognizes the theme. Include the compiled CSS or import from `assets/css/main.css`.
- Replace any static forms with WordPress-compatible solutions (Gravity Forms, Contact Form 7) and ensure any dynamic content (events, blog posts) use WP loops (`WP_Query`).
- If multilingual support or SEO is required, integrate plugins (WPML, Polylang, Yoast SEO) and ensure template hooks exist for plugin output.

### Theme-specific considerations

- Sanitize and escape all dynamic content with `esc_html`, `esc_attr`, and `wp_kses_post` to maintain security.
- Declare theme support features (`add_theme_support('title-tag')`, `add_theme_support('post-thumbnails')`) so WordPress manages metadata correctly.
- Provide Customizer options or block patterns for hero banners, CTA sections, and donation prompts so site administrators can make updates without editing code.
- Follow WordPress template hierarchy conventions so additional page templates (e.g., `single.php`, `archive.php`) can be added without refactoring.

## Design system

- **Primary red:** `#d20a13`
- **Primary blue:** `#0a78d1`

Use WCAG AA/AAA contrast checks when pairing colors with white or dark text. Accent shades should be derived with consistent saturation adjustments (e.g., 20% lighter for hover states) to maintain brand cohesion.

## Security and operational best practices

- Serve the site exclusively over **HTTPS** (GitHub Pages supports automatic TLS certificates).
- Implement **content moderation** workflows for any user-generated content (comments, guestbook, forms) to prevent defacement or abuse.
- Use **privacy-conscious analytics** (e.g., Matomo, Plausible) that respect local regulations and store data securely.
- Sanitize external embeds and disable inline scripts wherever possible to reduce injection risks.
- For external donation or volunteer forms, ensure the third-party provider is reputable, configure **webhooks** with secret validation, and monitor for phishing attempts.
- Maintain a documented **incident response plan** covering social account hijacking, misinformation takedowns, and DDoS mitigation.
- Keep dependencies (build tooling, npm packages) patched; use Dependabot or Renovate to automate updates.
- Store credentials (API keys, form endpoints) outside of the repository via environment variables or secret stores.

## Performance and build notes

- Prefer **WebP** (with JPEG/PNG fallbacks) for photography and **SVG** for vector logos to minimize file sizes.
- Minify CSS/JS bundles and enable tree-shaking if using a bundler (Vite, Webpack, esbuild).
- Inline critical CSS in `index.html`/`front-page.php` for the above-the-fold hero section while deferring non-critical styles.
- Use responsive image markup (`srcset`, `sizes`) so browsers select the optimal resolution.
- Audit accessibility and performance regularly with Lighthouse; target a 90+ performance score.
- Cache-bust static assets via filename hashing during the build step to prevent stale content when deploying updates.

With these conventions documented, future maintainers can confidently evolve the static site, port it to WordPress, and keep the campaign presence secure and performant.
