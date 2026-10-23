# Maggie Mac Beauty — site maintenance

**Live:** https://maggiemac.com.au (GitHub Pages, repo `Wemerson123/maggie-mac-atelier`, branch `main`, root). Push to `main` = deploy in ~1 minute.

## Where things live
- **Domain:** maggiemac.com.au — GoDaddy (Wemerson's account; each DNS save asks for an SMS code). DNS: `A @ 185.199.108.153`, `CNAME www → wemerson123.github.io`. HTTPS by GitHub Pages (Let's Encrypt, auto-renews).
- **Google Search Console:** URL-prefix property `https://maggiemac.com.au/`, verified by `googleb9bc054e50c62c78.html` — **never delete that file**.
- **Google Analytics 4:** account "Maggie Mac Beauty", property `maggiemac.com.au`, Measurement ID `G-YGJRZTE2DT` (inline script at the end of `index.html` and `privacy.html`). Events: `generate_lead` (booking / whatsapp), `social_click`, `get_directions`.
- **Google Business Profile:** "Maggie Mac Beauty", managed by the same Google account. Keep name, address, phone and hours identical to the site.
- **Bookings:** Acuity — https://app.acuityscheduling.com/schedule/47f589d2

## Editing
- Edit only in this repo (`~/dev/maggie-mac-site`). `~/dev/maggie-mac-beauty-v2` is an old draft without git.
- Contact details appear in: the Visit section, footer, floating Book button, the JSON-LD block in `<head>`, `privacy.html` and the Google Business Profile — change all of them together.
- New page → add it to `sitemap.xml` and resubmit in Search Console.
- Images: WebP, ≤ 2000px; add a smaller `-1000` variant + `srcset` for full-width images.
- Link previews (WhatsApp/Facebook) cache old images — test with `?v=2`.

## Backups
- Every change is a git commit (revert with `git revert <sha>`).
- Zip snapshots in `~/dev/backups/`.
