# email-campaigns-claude

Beautiful HTML email campaigns with [Resend](https://resend.com) and [Claude Code](https://claude.com/claude-code).

A Claude Code skill that encodes a complete pattern for building and sending modern marketing emails — asset hosting, reusable design blocks (frost-glass cards, video blocks with blurred backgrounds, CTA buttons), GIF optimization, and the Resend send pipeline.

![demo](./demo.gif)

## What's inside

- **[SKILL.md](SKILL.md)** — the full skill, drop into `~/.claude/skills/email-campaigns/` and invoke via `/email-campaigns`
- **[examples/claude-connector.html](examples/claude-connector.html)** — real production email built with this pattern. Open in a browser to see the rendered result.

## Install as a Claude Code skill

```bash
# Clone
git clone https://github.com/irinabuht12-oss/email-campaigns-claude.git

# Install as a skill
mkdir -p ~/.claude/skills/email-campaigns
cp email-campaigns-claude/SKILL.md ~/.claude/skills/email-campaigns/SKILL.md
```

Then in any Claude Code session: `/email-campaigns`

## What you'll learn

- **Asset hosting** — turn your own Vercel/Netlify `public/` folder into a free email CDN (no Cloudinary bill)
- **Design system** — copy-paste blocks: hero image, frost-glass logo cards, video thumbnails with blurred color backgrounds, dark CTA buttons
- **GIF pipeline** — ffmpeg one-liners to convert MP4 screen recordings into email-safe GIFs (under 2 MB, 15fps, sensibly trimmed)
- **Resend integration** — single-send, batch-send, drip sequences with webhooks
- **Pre-send checklist** — absolute URLs, preheader, alt text, client testing

## Design tokens at a glance

| Element | Value |
|---|---|
| Card corner radius | 3px |
| Card shadow | `0 2px 8px rgba(24,24,27,.06), 0 8px 24px rgba(24,24,27,.04)` |
| Typography scale | 26px / 15px / 13px |
| Max card width | 560px |
| Primary action | Black pill CTA, repeated 2x |
| Background | Soft sky-blue gradient + 6% SVG noise texture |

## Example

The `examples/` folder has a complete real-world email (the Ryze AI × Claude Connector launch) showing every block used in context.

![Example](./examples/preview.png)

Open `examples/claude-connector.html` in any browser for the live preview.

## License

MIT
