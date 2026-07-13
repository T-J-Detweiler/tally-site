# tally-site progress — site overhaul + Club Tally

Status snapshot so we can pick this up cold. Last worked: 2026-07-08.
**This is the `tally-site` repo only.** The `Tally` (app + worker) repo is being worked
in a separate session — do not touch it from here.

---

## Done and LIVE (pushed to main, verified on tallyapplications.com)

1. **One shared design system — `tally.css`.**
   Ported from the app's `:root` + `.btn-primary`. Both `index.html` and `club/index.html`
   link it; each keeps only page-specific CSS inline. The site used to invent its own look
   (solid-gradient CTAs the app doesn't have); it now speaks the app's language.

2. **Every button is the house button** (`.btn`): surface centre + gradient RING + gradient
   text via a child `<span>`. Owner hates solid-gradient buttons. `.btn-inv` (white ring,
   white ink) is the only button allowed on a full-bleed gradient banner.
   - JS label swaps go through `btnLabel()` — writing `btn.textContent` deletes the gradient
     span and the label goes invisible.

3. **Two gradients, one component.** `:root { --g: var(--grad) }`; `class="club"` swaps to
   `--grad-club` (purple → pink → GOLD `#FFD54A`, option C, sampled off `icon-club-C.png`).
   Every gradient inside a `.club` subtree re-tints for free.

4. **`/club` page** (`club/index.html`) — footer-linked only, never in the top nav.
   Three rungs: **Silver 15% / Gold 20% / Platinum 25%** + 5% override.
   **No subscriber thresholds** ("by invitation as your audience grows with us"), because
   thresholds are still unset. Copy follows the house rules: no em dashes, no "AI", no judgment.

6. **FP signup + login URLs are WIRED** (`CLUB_SIGNUP_URL` / `CLUB_LOGIN_URL`). The "Sign in to
   your dashboard" link renders as `href="#"` in the source and is rewritten on load, so it LOOKS
   dead to anyone reading the HTML. It isn't. Login page verified 200.

7. **The page shows dollars (2026-07-13).** "15% for life" is unpriceable without the sticker
   price, so `.tier-math` does the worked example a creator actually cares about. Two facts it must
   keep straight: Tally is **$129.99/yr**, and commission pays on **what the customer PAYS, not
   sticker** — with a 15% code that's $110.49, so Silver earns ~$16.57/person/year, not $19.50.
   The 15% code on the page is an EXAMPLE (the standard member discount is still an open decision
   below); if that standard lands somewhere else, the perk copy and `.tier-math` both change.

8. **Contact is `micah.tarter@tallyapplications.com`**, not the personal Gmail, on `/club` AND the
   homepage footer. A page whose job is convincing strangers you'll pay them for years cannot ask
   them to email a Gmail.

9. **The App Store attribution question is answered honestly, not papered over.** There is NO
   deferred deep linking, and a referred person who installs from the App Store and pays via IAP
   earns their creator NOTHING (see the store-badge comment in `index.html`). So step 2 of "How it
   actually works" says plainly that their people sign up on the WEB, where the discount lives and
   the referral sticks. Never claim store installs are tracked. They are not.

5. **Homepage free-trial popup bug FIXED.** It was the 60-day `_fprom_ref` cookie. `refWelcome()`
   auto-opened on `fpRef()`, which reads the cookie first, so every referred visitor got the
   trial sheet on every visit for two months. Now gated on a FRESH `?fpr=` arrival, snapshotted
   in `<head>` before `t.js` strips the param; never the cookie, never for signed-in visitors.
   The cookie still drives the greeting chip + discount prefill. Verified in a real browser,
   old vs new, with a seeded cookie.

The FirstPromoter branding kit (colors, logo generator, portal copy, plan limits) lives in the
**Tally** repo at `store-assets/club-portal/BRANDING.md` — it was committed there before the
other session took that repo over. Read it there, don't recreate it here.

---

## Waiting on the owner (blockers — nothing else moves until these)

1. **Plan check for the embedded signup form.** FP's docs are explicit: the embeddable signup
   form needs the **Business plan AND a custom domain** first. The `/club` page link-outs today;
   swapping to the `<iframe>` embed is a 3-line change, documented inline in `club/index.html`.

2. **Portal CSS not written.** Making the FP portal itself pixel-match Tally needs custom CSS
   against FP's DOM, which shouldn't be guessed. Paste the public signup-page URL and it can be
   done properly.

---

## Open decisions (no rush)

- **The standard member discount.** The page currently uses **15% off as an EXAMPLE**. Each
  promoter's real coupon comes from FirstPromoter's own records (`/promoter-info`), so until there
  is one standard code for new members, the page cannot promise a specific number as fact.
- **Tier thresholds.** Deliberately unstated on the page. First cohort is the calibration
  instrument (owner's framing).
- **Club visibility.** Currently footer-link only (owner's choice). Nav link is a one-line add
  if that changes.

---

## Where the pieces are

| Thing | File |
|---|---|
| Shared design system | `tally.css` |
| Homepage + signup funnel | `index.html` |
| Club Tally page | `club/index.html` |
| FP signup/login URL slots | bottom of `club/index.html` (`CLUB_SIGNUP_URL`, `CLUB_LOGIN_URL`) |
| Embed-swap instructions | inline comment in `club/index.html` (`#join` section) |
| FP tracking script (self-hosted) | `t.js` |
| Portal branding kit + logos | **Tally** repo → `store-assets/club-portal/` |
