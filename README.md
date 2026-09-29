# me

A small, static **digital business card** for Tomer Klein, hosted on GitHub Pages at
**<https://me.tomerklein.dev>**.

The page is a single HTML file generated with [EnBizCard](https://enbizcard.vishnuraghav.com/)
(an open-source digital business card generator by Vishnu Raghav). It shows a photo, name, job
title and company, a **Save Contact** button that downloads a vCard, quick-contact buttons, social
links, and a QR code that points back to the card. There is no build step, no backend and no
tracking.

## Features

- **Profile header**: photo (`photo.jpeg`), name, job title and organization.
- **Save Contact**: downloads `tomerklein.vcf`, a vCard 3.0 file that phones and address books
  can import. The card holds the name, organization, title, a work email, and website,
  WhatsApp, GitHub, LinkedIn and blog links. Phone, business-card URL, PGP key and note fields
  are present but empty.
- **Primary actions**: Email (`mailto:`), Website and WhatsApp buttons.
- **Secondary links**: GitHub, LinkedIn and a blog link (labelled "Medium", pointing to
  `tomerklein.dev`).
- **Share button** (top-right, left of the QR button): if the browser supports the Web Share
  API (checked with `navigator.canShare`), it calls `navigator.share`. Otherwise it opens a
  dialog with a **Copy URL** button that copies the page address to the clipboard.
- **QR code button** (top-right corner): opens a dialog with a QR code of the **current page URL**
  (`window.location.href`), so the card can be opened on another device.
- **No external requests in the source**: the repository's HTML loads no web fonts, CDNs or
  analytics. It uses the system `sans-serif` font, a local stylesheet and a local QR code
  script. (The live site gets a few Cloudflare additions; see [Live site](#live-site).)
- `noindex, nofollow` robots meta tag, so search engines are asked not to index the card.

## Live site

| Item | Value |
|---|---|
| URL | <https://me.tomerklein.dev> |
| Custom domain | `me.tomerklein.dev` (from the [`CNAME`](CNAME) file) |
| Hosting | GitHub Pages, behind a proxied Cloudflare record |

Cloudflare changes what visitors receive compared with the repository source:

- It rewrites the `mailto:` link and injects its email-obfuscation script
  (`/cdn-cgi/scripts/.../email-decode.min.js`).
- It adds a NEL (`report-to`) reporting header.
- Plain `http://me.tomerklein.dev` is served without redirecting to HTTPS. **Copy URL** and
  the Web Share button only work over HTTPS, so they fail on the HTTP version.

## How it works

| File | Purpose |
|---|---|
| [`index.html`](index.html) | The whole card: markup, inline styles and inline scripts (share, copy URL, QR dialog). |
| [`style.min.css`](style.min.css) | Minified layout and component styles. |
| [`qrcode.min.js`](qrcode.min.js) | Minified QR code generator that renders an SVG in the browser. |
| [`photo.jpeg`](photo.jpeg) | Profile photo. |
| [`tomerklein.vcf`](tomerklein.vcf) | vCard downloaded by **Save Contact**. |
| [`CNAME`](CNAME) | Custom domain for GitHub Pages. |

On page load, the script shows the top action buttons and renders the QR code:

```js
new QRCode({ content: window.location.href, container: "svg-viewbox",
             join: true, ecl: "L", padding: 0 }).svg()
```

A small inline script also redirects `…/path` to `…/path/` (adds a trailing slash) so the
relative asset paths resolve.

## Use it as a template

1. Fork or copy the repository.
2. Replace `photo.jpeg` with your own photo (keep the name, or update `src="./photo.jpeg"` in
   `index.html`).
3. Edit `index.html`:
   - `<title>`, `og:title` and `twitter:title`: `<Your Name>'s Digital Business Card`.
   - The `.name`, `.jobtitle` and `.bizname` paragraphs.
   - The `href` of each action button: `mailto:<you@example.com>`, your website,
     `https://wa.me/<international-number-without-plus>`, and your GitHub, LinkedIn and blog URLs.
   - The **Save Contact** link (`href="tomerklein.vcf"`) if you rename the vCard.
   - The hidden **Download Key** dialog (`#keyView`): its `dlKey` link points to
     `./Tomer Klein's public key.asc`, which is not in the repository, and no button opens the
     dialog. Remove the block, or add your own `.asc` key and a button with `id="showKey"`.
   - Colors: the page uses `rgb(18, 42, 35)` / `#122A23` (dark green) and `rgb(221, 221, 221)`
     (card background) in inline styles.
4. Replace the vCard with your own `<your-name>.vcf`, for example:

   ```text
   BEGIN:VCARD
   VERSION:3.0
   N:<Last>;<First>;;;
   FN:<First Last>
   ORG:<Company>
   TITLE:<Job title>
   TEL;TYPE=CELL:<+000000000000>
   EMAIL;TYPE=WORK:<you@example.com>
   URL:<https://card.example.com>
   END:VCARD
   ```

5. Put your own domain in `CNAME` (for example `card.example.com`), or delete the file to use
   `https://<username>.github.io/<repo>/`.

Everything in the vCard and in `index.html` is public once published. Only include contact
details you are happy to share with anyone who has the link.

Alternatively, generate a new card with [EnBizCard](https://enbizcard.vishnuraghav.com/) and
replace the files.

## Local preview

```bash
python3 -m http.server 8000
# open http://localhost:8000/
```

The Web Share API and the clipboard (**Copy URL**) only work in a secure context
(HTTPS or `localhost`).

## Deploy with GitHub Pages

1. In the repository go to **Settings → Pages**.
2. Set **Source** to *Deploy from a branch*, and choose the default branch and `/ (root)`.
3. For a custom domain, keep the domain in `CNAME` and create a DNS `CNAME` record pointing
   `me` (or your subdomain) to `<username>.github.io`.
4. HTTPS redirect:
   - If the DNS record is **not proxied**, enable **Enforce HTTPS** in **Settings → Pages**
     once the certificate is issued.
   - If the record is **proxied through Cloudflare** (as this site is), set the redirect in
     Cloudflare instead: **SSL/TLS → Edge Certificates → Always Use HTTPS**.

Every push to the default branch republishes the site.

## Credits

- [EnBizCard](https://enbizcard.vishnuraghav.com/) by Vishnu Raghav: generated the page
  layout, styles and scripts. EnBizCard is licensed under **AGPL-3.0**.
- `qrcode.min.js`: [qrcode-svg](https://github.com/papnkukn/qrcode-svg) v1.1.0 by papnkukn
  (MIT license), re-minified without its license header.
- Icons are inline SVGs from the EnBizCard template.

## License

This repository has **no `LICENSE` file**, so no license is granted for its content (the
photo and personal details are not meant for reuse). The bundled `qrcode-svg` library is
under its own MIT license, and the page is generated from EnBizCard, which is AGPL-3.0.
<!-- TODO: verify — owner to decide how EnBizCard's AGPL-3.0 affects this repo's licensing (e.g. add a LICENSE) -->
