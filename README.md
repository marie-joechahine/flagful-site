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

Check the app's shipped behavior and `src/lib/featureFlags.ts` when reviewing
policy claims. The current release has sharing, gameplay analytics and
third-party error reporting off for everyone. `docs/compliance.md` contains
background research; older beta assumptions in it are not release evidence.

> This is not legal advice. Have counsel review the text before it is published,
> particularly §5 (legal bases), §6 (children) and §7 (retention).

## Fill these in before publishing

Every one appears on the page as a loud amber `[[PLACEHOLDER]]`. Search the repo
for `[[` to find any you missed — if one is still visible on the live site, it is
meant to be obvious.

| Placeholder                                        | What it needs                                                                                                                                          |
| -------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `[[PUBLISH_DATE]]`                                 | Actual effective date; update the separate last-updated date on later edits.                                                                           |
| `[[LEGAL_ENTITY]]`                                 | Publisher name matching the store listing.                                                                                                             |
| `[[POSTAL_ADDRESS]]`                               | Publisher's contactable postal address.                                                                                                                |
| `[[CONTACT_EMAIL]]`                                | Monitored support and privacy address.                                                                                                                 |
| `[[EMAIL_PROVIDER]]`                               | The service actually handling support correspondence.                                                                                                  |
| `[[UPDATE_PROCESSING_LEGAL_BASIS]]`                | Confirm the lawful basis for the actual update processing; do not assume installing the app supplies consent or a contract with a child.               |
| `[[CORRESPONDENCE_RETENTION]]`                     | Actual support/privacy-request retention periods, including justified exceptions.                                                                      |
| `[[UPDATE_RECORD_RETENTION]]`                      | Confirm technical record retention with Expo under the applicable service terms.                                                                       |
| `[[PROCESSING_LOCATIONS_AND_TRANSFER_SAFEGUARDS]]` | Verify provider locations and applicable transfer safeguards, including the email provider. Do not claim agreements have been signed without checking. |
| `[[EU_REPRESENTATIVE_BLOCK_OR_DELETE]]`            | Representative details if required; otherwise remove the paragraph.                                                                                    |

## Before publishing

This PR describes the release with analytics, error reporting and sharing off
for everyone, including children. It does not publish the app or change store
audience declarations. The pages remain a draft until the placeholders and
checks below are resolved.

- [ ] Ship and verify the build with `ANALYTICS_ENABLED`,
      `ERROR_REPORTING_ENABLED` and `SHARING_ENABLED` all `false`. Confirm no
      analytics or reporting requests on startup, during play or after errors,
      including an upgrade from an older build and offline/relaunch behavior.
- [ ] Confirm the actual update request data and retention, applicable provider
      terms, support email handling and transfer safeguards; fill the placeholders.
- [ ] Check whether earlier test builds collected data that is still retained.
      Disabling SDK startup does not delete remote records or old local queues.
      If records remain, add a version/date-specific historical-data disclosure
      with its purpose, providers, retention and deletion process before publishing.
      If no records remain, document that verification privately. This PR does
      not claim to have deleted or inspected those records.
- [ ] Check any older builds still distributed or used by testers. Do not apply
      the new no-analytics statement to a version that still sends events.
- [ ] Align Play Data safety, App Store privacy disclosures and target-audience
      declarations with the versions actually distributed. Google Play's form
      covers all versions currently distributed under the package name.
- [ ] Confirm the operating-system/app-store diagnostics available to the
      publisher; describe them separately if you use or retain those reports.
- [ ] Keep the privacy link accessible in the app and both store listings.
- [ ] Review both public policies and the home page together; resolve all `[[...]]`
      placeholders before merging to `main`, which publishes the site.

## If collection or sharing changes later

Update the policy and store disclosures before releasing the change. Implement
any required notice, age/eligibility checks and user or parental consent before
collection starts. Updating this page alone does not authorize collection.
Review old SDK queues before re-enabling reporting so old events are not uploaded
under a new permission decision. Add active providers only when their processing
is actually part of the service; the public pages need no inventory of unused SDKs.

## Sources checked for this revision

- [Google Play User Data policy](https://support.google.com/googleplay/android-developer/answer/10144311?hl=en)
- [Google Play Data safety guidance](https://support.google.com/googleplay/android-developer/answer/10787469?hl=en)
- [Expo privacy explained](https://expo.dev/privacy-explained)
- [GitHub privacy statement](https://docs.github.com/en/site-policy/privacy-policies/github-general-privacy-statement)

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
