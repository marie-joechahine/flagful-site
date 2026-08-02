# Flagful site

Static site serving the privacy policy required for Google Play and App Store
submission. Plain HTML and one stylesheet — no build step, no dependencies, no
third-party requests (fonts included, deliberately).

## Design

The pages use the app's notebook chassis: warm cream paper with a fibre texture,
a spiral binding gutter with punched rings, dashed cut-lines, and parchment
callouts. Palette, radii and the shadow in `style.css` are transcribed from
`src/theme/tokens.ts` in the app repo — if a token moves there, move it here too.

Two rules inherited from the app's visual language are worth preserving:

- **One shadow language.** Every raised surface takes `--shadow`. Nothing gets a
  bespoke drop shadow.
- **Colour is never the only signal.** The ✕ and ✓ lists differ by glyph as well
  as tint, so they still read without colour vision.

## Assets

`assets/` holds web-sized derivatives of first-party artwork from the app repo —
Flaggy, the logo, the paper texture and three icons — re-encoded to WebP at
display size (1024px originals down to 10–30 KB each). The whole directory is
~400 KB including fonts.

Fonts are **self-hosted**: Crimson Pro 700 for headings, Nunito 400/700 for body,
converted from the app's bundled TTFs to WOFF2. Requesting them from Google would
send every visitor's IP address to Google, which is a poor look on a privacy
policy and has been held to breach the GDPR in the EU. Both are SIL Open Font
License; the licence text ships alongside them in `assets/fonts/`, which also
satisfies part of the attribution work tracked in `docs/compliance.md` §5.6.

To regenerate the images after changing the originals, run a `sharp` resize from
the **app** repo (it has the dependency): widths 400 for the logo, 320–560 for
the mascots, 900 for the texture at quality 52, 96–128 for icons.

Source of truth for the policy's factual claims is `docs/compliance.md` §3 in the
app repo, which was written by reading the code.

> This is not legal advice. Have counsel review the text before it is published,
> particularly §5 (legal bases), §6 (children) and §7 (retention).

## Fill these in before publishing

Every one appears on the page as a loud amber `[[PLACEHOLDER]]`. Search the repo
for `[[` to find any you missed — if one is still visible on the live site, it is
meant to be obvious.

| Placeholder                             | Where                                     | What it needs                                                                                            |
| --------------------------------------- | ----------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| `[[PUBLISH_DATE]]`                      | privacy.html (×2)                          | The date you publish, e.g. `2 August 2026`. Update the second one on every later edit.                    |
| `[[LEGAL_ENTITY]]`                      | privacy.html                              | The legal publisher name that will appear in Play Console.                                               |
| `[[POSTAL_ADDRESS]]`                    | privacy.html                              | A contactable postal address. Required by GDPR Art. 13; Play shows a developer address on public listings. |
| `[[CONTACT_EMAIL]]`                     | privacy.html (×3), privacy-kids.html, index.html | A monitored address. This is where parental deletion requests arrive.                            |
| `[[ANALYTICS_LEGAL_BASIS]]`             | privacy.html §5                           | **Blocked** — see below.                                                                                  |
| `[[POSTHOG_RETENTION]]`                 | privacy.html §7                           | A concrete period, e.g. `12 months`. Set it in the PostHog project first, then state it here.             |
| `[[SENTRY_RETENTION]]`                  | privacy.html §7                           | A concrete period. Sentry's default is 90 days; confirm on your plan.                                     |
| `[[EU_REPRESENTATIVE_BLOCK_OR_DELETE]]` | privacy.html §12                          | Name and address of a GDPR Art. 27 representative if one is required, otherwise delete the paragraph.     |

### `[[ANALYTICS_LEGAL_BASIS]]` is blocked on a product decision

This one cannot be filled honestly yet. `docs/compliance.md` §4.3 is still open:
analytics currently start on first launch with no notice, no consent and no
opt-out, while storing a persistent device identifier.

- Claiming **consent** would be false — nothing in the app asks for it.
- Claiming **legitimate interests** is legally weak, because ePrivacy Art. 5(3)
  requires consent to store or read an identifier on a device regardless of which
  GDPR basis you pick, and a child cannot give valid consent anyway.

Resolve §4.3 first (the Path A / Path B decision in §2 of that document), then
write this line to match what the app actually does. If Path A lands and the
persistent identifier goes away, several other paragraphs in §3.1, §6 and §8 get
shorter and stronger too.

Everything else on the page can be published today.

## Local preview

```powershell
python -m http.server 8000
```

Then open <http://localhost:8000/privacy.html>. Any static server works; the site
has no server-side logic.

## Deploying

Pages is configured to deploy from the `main` branch, root directory. Pushing to
`main` publishes within a minute or two.

### Custom domain

1. Buy the domain.
2. At the DNS provider, add four `A` records for the apex, pointing at
   `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`,
   and a `CNAME` for `www` pointing at `marie-joechahine.github.io`.
3. In the repo: Settings → Pages → Custom domain → enter the domain → Save.
   GitHub writes the `CNAME` file itself and verifies DNS.
4. Wait for the certificate, then tick **Enforce HTTPS**.

Do not commit a `CNAME` file by hand before the DNS records resolve — Pages will
start serving on a domain that does not answer, and the `github.io` URL stops
working in the meantime.

## Where this URL gets used

- Play Console → App content → Privacy policy
- Play Console → Store listing
- App Store Connect → App Privacy
- A link row in the app's own Settings (`SettingsPopup.tsx` in the app repo) —
  reviewers on a child-audience app look for it in-app, not only in the listing
