---
name: email-campaigns
version: 1.0.0
description: |
  Design, build, and send beautiful HTML email campaigns via Resend. Handles asset hosting (images + GIFs on your own CDN), the pre-built email HTML component library (frost-glass cards, hero image, video blocks with blurred backgrounds, CTA buttons, footer), GIF optimization from MP4 source video, and sending via the Resend API. Use when asked to "create an email", "draft a marketing email", "send a campaign", "make an email sequence", or "build an email with Resend".
  Proactively suggest when the user mentions sending marketing emails, launching a feature via email, or designing a newsletter.
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Grep
  - Glob
---

# Email Campaigns

Build modern, on-brand marketing emails and send them via Resend. This skill encodes a complete pattern: asset hosting, reusable HTML components, GIF tooling, and the Resend send pipeline.

## 1. Asset hosting — use your own site as a CDN

**Setup once.** In a Next.js or any static-hosted project, create a folder served at the site root:

```
<project>/public/email-assets/
  <campaign-name>/
    hero.png
    demo.gif
```

Anything in `public/` is served at `https://yourdomain.com/email-assets/...`. Commit + push = instant CDN. No Cloudinary, no S3.

**Rules for email-safe assets:**
- Naming: lowercase, dashes only (`hero-image.jpg` ✅, `Hero Image.jpg` ❌)
- Images: JPG/PNG, ≤ 1 MB, max 600px wide
- GIFs: ≤ 1.5 MB, max 720px wide, 12-15 fps, keep under 8 seconds
- Video: MP4 in same folder, but NEVER embed `<video>` — always use GIF thumbnail linking to MP4
- Version filenames instead of overwriting (`hero-v2.jpg`) — email clients cache aggressively

**Before sending:** swap all local paths to absolute URLs (`https://yourdomain.com/email-assets/...`). Relative paths work in browser preview but break in recipients' inboxes.

## 2. Design system — reusable blocks

Use this building-block inventory. Copy from a previous campaign, don't redesign from scratch.

### Outer shell

```html
<body style="margin:0; padding:0; background-color:#ffffff;
             font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Helvetica,Arial,sans-serif;">
  <div style="display:none; max-height:0; overflow:hidden; font-size:1px; line-height:1px; color:#fff;">
    [PREHEADER — 1 sentence, shown in inbox preview]
  </div>
  <table width="100%" cellpadding="0" cellspacing="0" border="0" style="background-color:#ffffff;">
    <tr><td align="center" style="padding:32px 16px;">
      <table width="560" cellpadding="0" cellspacing="0" border="0"
             style="max-width:560px; width:100%;">
        <!-- MAIN CARD -->
        <tr><td>
          <table width="100%" cellpadding="0" cellspacing="0" border="0"
                 style="background-color:#f7fbfe;
                        background-image: url('data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyMDAiIGhlaWdodD0iMjAwIj48ZmlsdGVyIGlkPSJuIj48ZmVUdXJidWxlbmNlIHR5cGU9ImZyYWN0YWxOb2lzZSIgYmFzZUZyZXF1ZW5jeT0iMC44NSIgbnVtT2N0YXZlcz0iMiIgc3RpdGNoVGlsZXM9InN0aXRjaCIvPjwvZmlsdGVyPjxyZWN0IHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIGZpbHRlcj0idXJsKCNuKSIgb3BhY2l0eT0iMC4wNiIvPjwvc3ZnPg=='),
                                         linear-gradient(90deg, #edf6fd 0%, #f6fbfe 50%, #fcfdfe 100%);
                        border-radius:4px;
                        box-shadow: 0 2px 8px rgba(24,24,27,0.06), 0 8px 24px rgba(24,24,27,0.04);
                        overflow:hidden;">
            <!-- sections go here -->
          </table>
        </td></tr>
      </table>
    </td></tr>
  </table>
</body>
```

Key tokens:
- **Card bg:** light sky gradient + 6% SVG noise texture for depth (subtle grain). Gradient direction `90deg` = left→right.
- **Card shadow:** layered soft shadow, no border
- **Card radius:** 4px (tight corners feel modern)

### Header image
Full-width image flush with card top. Crop the top 3/4 of a wide image (1376×576 cropped from 1376×768) for hero; use the bottom 1/3 (256px) as a footer image inside the card.

### Logo cards (frost-glass)

Five logos distributed across full card width via `width="20%"` columns. Each card 72×72, light fill (#f4f4f7 gradient), soft shadow, 3px radius.

### Hero title + body
```html
<h1 style="font-size:26px; font-weight:700; color:#18181b; letter-spacing:-0.5px;">...</h1>
<p style="font-size:15px; line-height:1.65; color:#3f3f46;">...</p>
```

### Feature list with black bullets (not green checks)
Keep bullets black `&bull;` in a 20px cell, text 15px. Avoid green check pills — they look dated.

### Video block (frost-glass with blurred bg)

Pre-blur your brand hero image with ffmpeg to use as a colorful bed behind videos:
```bash
ffmpeg -y -i header.png -vf "boxblur=luma_radius=30:luma_power=3" header_blur.png
```

Then:
```html
<a href="LANDING-URL">
  <p>Video title</p>
  <table width="100%">
    <tr><td style="background-image:url('https://yourdomain.com/email-assets/header_blur.png');
                   background-size:cover; border-radius:3px; padding:14px;
                   box-shadow:0 4px 14px rgba(24,24,27,0.1);">
      <table width="100%">
        <tr><td style="background-color:rgba(255,255,255,0.55);
                       backdrop-filter:blur(12px);
                       -webkit-backdrop-filter:blur(12px);
                       border:1px solid rgba(255,255,255,0.6);
                       border-radius:3px; padding:10px;
                       box-shadow:inset 0 1px 0 rgba(255,255,255,0.5);">
          <img src="GIF-URL" width="475" style="display:block; width:100%;
                                                max-width:475px; border-radius:3px;">
        </td></tr>
      </table>
    </td></tr>
  </table>
</a>
```

In Apple Mail/iOS: `backdrop-filter` renders real glass. In Gmail/Outlook: the pre-blurred image underneath sells the effect on its own.

### CTA button
Black pill, 4px radius, 14px 24px padding, optional small logo inline:
```html
<table width="100%"><tr><td align="center"
       style="border-radius:4px; background-color:#18181b;">
  <a href="LANDING-URL" style="display:block; padding:14px 24px;
                               font-size:14px; font-weight:600; color:#fff;
                               text-decoration:none;">
    <img src="LOGO" width="20" height="20"
         style="display:inline-block; vertical-align:middle;
                margin-right:8px; border-radius:3px;" alt="">
    <span style="vertical-align:middle;">Connect your accounts &rarr;</span>
  </a>
</td></tr></table>
```

Place one CTA immediately after the first proof video (highest conversion moment) and one at the bottom.

### Typography scale
- H1: 26px
- Body/features/video titles: 15px
- Section labels/footer: 13px
- CTA button: 14px

Stick to this. No more than 3 sizes.

## 3. GIF optimization workflow

For each screen recording:

```bash
# Convert MP4 → optimized GIF (720px, 15fps, 2x speed, trim to 8s)
ffmpeg -y -i source.mp4 -t 8 \
  -vf "setpts=0.5*PTS,fps=15,scale=720:-1:flags=lanczos,split[s0][s1];\
       [s0]palettegen=max_colors=256[p];\
       [s1][p]paletteuse=dither=sierra2_4a" \
  -loop 0 output.gif

# Check file size — should be under 1.5 MB
ls -lh output.gif
```

If too big:
- Drop to 12fps
- Reduce width to 600px
- Trim to 5-6 seconds
- Drop palette to 128 colors, use `dither=bayer:bayer_scale=5`

If too low quality:
- Bump to 720-900px
- 256 colors with `sierra2_4a` dither
- Accept 1.5-2 MB ceiling

## 4. Sending via Resend

### Setup (once per project)

```bash
npm install resend
```

Env var: `RESEND_API_KEY=re_...` (get from resend.com dashboard)

From domain: verify your sending domain in Resend settings first (DNS records take ~15 min to propagate). Use `hello@yourdomain.com`, not a free provider.

### Send a single test email

```js
// send.mjs
import { Resend } from 'resend';
import fs from 'fs';

const resend = new Resend(process.env.RESEND_API_KEY);
const html = fs.readFileSync('./email.html', 'utf-8');

const { data, error } = await resend.emails.send({
  from: 'Ira <hello@get-ryze.ai>',
  to: 'you@example.com',
  subject: 'Claude Connector for Google/Meta Ads is live',
  html,
  // Optional plain-text fallback
  text: 'We released the Claude connector. Connect in 1 click: https://...',
});

console.log(error ?? data);
```

Run: `node send.mjs`. Always send to yourself first.

### Send to a list (batches)

Resend rate-limits at 10 req/sec (free) or 100 req/sec (paid). For larger lists use their batch API:

```js
await resend.batch.send(
  recipients.map(email => ({
    from: 'Ira <hello@get-ryze.ai>',
    to: email,
    subject: '...',
    html,
  }))
);
```

Up to 100 emails per batch call. Chunk your list accordingly.

### Sequences / drip campaigns

For a 3-email sequence (day 0, day 3, day 7), use a cron + a sent-state table in Postgres/Supabase:

```js
// Pseudo
const dueToday = await db.campaigns.findMany({
  where: { scheduled_date: today, status: 'pending' }
});
for (const c of dueToday) {
  await resend.emails.send({ ...c });
  await db.campaigns.update({ where: { id: c.id }, data: { status: 'sent' } });
}
```

Resend has webhooks for `email.delivered`, `email.opened`, `email.clicked` — use them to trigger the next email in the sequence rather than fixed timers.

## 5. Pre-send checklist

Before you hit send on a real list:

- [ ] All asset URLs are absolute (`https://...`), not relative (`/email-assets/...`)
- [ ] Preheader text set (first hidden div) — this is the inbox preview line
- [ ] Subject line tested for deliverability (no ALL CAPS, no exclamation spam)
- [ ] Every image has meaningful `alt` text
- [ ] Every link has `target="_blank"`
- [ ] GIFs under 2 MB each, total email under 5 MB
- [ ] Tested in at least: Gmail web, Gmail mobile, Apple Mail, Outlook
- [ ] Unsubscribe link in footer (required by CAN-SPAM / GDPR)
- [ ] Sent one test to yourself — clicked every link
- [ ] `from` address is on a verified domain in Resend

## 6. Conversion best practices

- **One primary CTA** — repeated twice (mid-email + bottom). Don't dilute with secondary actions.
- **Video first, text second** — a 3-second GIF shows product faster than 3 paragraphs.
- **Specificity wins** — "Connect in 1 click" > "Get started". "30-second setup" > "Easy to install".
- **Claim + proof** — every claim near a visual (GIF, screenshot, or logo strip showing integrations).
- **Low-friction reply CTA** — "Reply 'Guide' and I'll send you X" is high-intent and cheap to fulfill.
- **Send Tuesday-Thursday, 9-11am recipient-local**. Avoid Monday (full inboxes) and weekends (low open).

## 7. Template starter

To bootstrap a new campaign, copy the most recent working HTML from your repo (e.g. `public/email-assets/preview/claude-connector.html`) and edit in place. Keep the preview file checked in alongside the campaign folder:

```
public/email-assets/2026-05-launch/
  hero.png
  demo.gif
  preview.html
```

Preview locally at `http://localhost:3000/email-assets/2026-05-launch/preview.html`.

## 8. What NOT to do

- ❌ Don't use `<video>` tags — blocked in Gmail/Outlook
- ❌ Don't use CSS grid or flexbox — broken in Outlook. Use nested `<table>`s.
- ❌ Don't use web fonts — most clients strip them. Stick to system font stack.
- ❌ Don't use background images on the `<body>` if you need them on mobile Gmail — it ignores body bg. Put color inside the main card.
- ❌ Don't embed MP4 directly — always GIF preview → link to external MP4.
- ❌ Don't send without testing — render one in Litmus/Mailtrap or just send to yourself on 3 clients.
