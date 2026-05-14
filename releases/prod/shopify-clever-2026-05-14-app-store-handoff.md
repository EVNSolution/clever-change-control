# shopify-clever App Store approval handoff — 2026-05-14

## Purpose

Record the production release and approval-handoff evidence for the `clever`
Shopify app. This is the change-control release evidence pointer for the manual
Partner Dashboard submission lane.

## Trace identifiers

- Change id: `chg-20260514-001`
- Change-control issue: EVNSolution/clever-change-control#211
- Target repository: EVNSolution/shopify-clever
- Target tracking issue: EVNSolution/shopify-clever#6
- Target repository trace PRs:
  - EVNSolution/shopify-clever#4 — factual App Store listing draft
  - EVNSolution/shopify-clever#5 — latest readiness evidence docs
  - EVNSolution/shopify-clever#7 — dashboard trace links in approval docs
  - EVNSolution/shopify-clever#8 — verifier-enforced dashboard trace
  - EVNSolution/shopify-clever#9 — 66-check verifier evidence docs
  - EVNSolution/shopify-clever#10 — dashboard submission evidence template
  - EVNSolution/shopify-clever#11 — production bundle release evidence docs
  - EVNSolution/shopify-clever#12 — final approval plan evidence
  - EVNSolution/shopify-clever#13 — stable final plan trace wording
  - EVNSolution/shopify-clever#14 — detailed Shopify AI self-review evidence
  - EVNSolution/shopify-clever#15 — production release evidence synchronization

## Runtime release evidence

- Runtime production source commit: `0d05a46295e499ffeb22d057b6b7e2ca789262de`
- Runtime CI evidence: https://github.com/EVNSolution/shopify-clever/actions/runs/25852472566
- Production host: EC2 `i-0133358f86590294f`, EIP `3.39.216.177`
- Production deploy path: `/srv/shopify-clever`
- Production admin URL: `https://clever-admin.3-39-216-177.sslip.io`
- Production delivery API URL: `https://clever-delivery.3-39-216-177.sslip.io`
- Shopify app version: `compliance-20260514-0d05a46`
- Shopify version ID: `gid://shopify/Version/963177807873`
- Version status observed by Shopify CLI: `active`

## Latest repository readiness evidence

- Latest target main at handoff: `5ff388b53c421f5761b52ddc16a468ec5cc387b5`
- Deployed production bundle commit: `16223b062079af5632b80d1bf08abd5bf775f0be`
- Production-release evidence docs commit: `0b37a6dcd2644e7a6c66d3d002659489891db776`
- Final approval plan evidence commit: `5ff388b53c421f5761b52ddc16a468ec5cc387b5`
- PR CI evidence for latest verifier update: https://github.com/EVNSolution/shopify-clever/actions/runs/25858058940
- Main CI evidence for latest verifier update: https://github.com/EVNSolution/shopify-clever/actions/runs/25858126053
- Production workflow evidence for deployed bundle: https://github.com/EVNSolution/shopify-clever/actions/runs/25856190483
- Detailed AI self-review PR CI: https://github.com/EVNSolution/shopify-clever/actions/runs/25857796867
- Detailed AI self-review main CI: https://github.com/EVNSolution/shopify-clever/actions/runs/25857865303
- Readiness command: `npm run check:shopify-submission`
- Readiness result: `shopify-submission-readiness-ok`, `104` checks

## Production smoke evidence

The production bundle was rebuilt/restarted on EC2, then smoke tested:

- `https://clever-delivery.3-39-216-177.sslip.io/healthz` returned 200.
- `https://clever-delivery.3-39-216-177.sslip.io/readyz` returned 200.
- `https://clever-admin.3-39-216-177.sslip.io/auth/login` returned 200.
- The admin HTML included Shopify App Bridge CDN script.
- The admin HTML included `shopify-api-key` meta.
- Invalid compliance webhook HMAC smoke returned 401.

Operational note: GitHub `workflow_dispatch` run 25856190483 validated successfully but
its EC2 deploy job could not reach SSH port 22 from the GitHub runner. The production
bundle `b64fa2c8ebcf0bf5cb6e9eebc04450e557fa9d01` was therefore deployed from a clean
local git archive through a temporary current-IP-only SSH security-group rule. A later
production refresh deployed `16223b062079af5632b80d1bf08abd5bf775f0be` to `/srv/shopify-clever`
and recorded it in `.release-sha`; temporary current-IP SSH ingress `1.231.151.99/32` was revoked after deploy. The earlier run
25854536246 showed the same runner SSH reachability limitation for the previous
release attempt.

## Prepared target repository artifacts

- `docs/shopify-app-store-approval-report.md`
- `docs/shopify-app-store-completion-audit.md`
- `docs/shopify-partner-dashboard-submission-packet.md`
- `docs/shopify-ai-self-review-detail.md`
- `docs/shopify-app-store-listing-draft.md`
- `docs/shopify-protected-customer-data-field-map.md`
- `docs/shopify-privacy-policy-draft.md`
- `docs/shopify-app-store-assets/clever-app-icon-1200.png`
- `docs/shopify-app-store-assets/screenshot-and-screencast-shotlist.md`
- `scripts/check-shopify-submission-readiness.mjs`

Additional trace comments:

- EVNSolution/shopify-clever#6 comment: https://github.com/EVNSolution/shopify-clever/issues/6#issuecomment-4450378623
- EVNSolution/clever-change-control#211 comment: https://github.com/EVNSolution/clever-change-control/issues/211#issuecomment-4450378736

## Remaining manual Partner Dashboard blockers

The target repository lane is complete, but the Shopify App Store objective is not
complete until the following externally authorized steps are done and evidence is
posted to EVNSolution/shopify-clever#6 and EVNSolution/clever-change-control#211:

- [ ] Submit protected customer data request for protected order data, name, address, and phone.
- [ ] Publish final legal/business-approved privacy policy URL and enter it in Partner Dashboard.
- [ ] Upload prepared app icon, final screenshots, and screencast.
- [ ] Enter support, API, emergency, and reviewer contact details.
- [ ] Select free plan/pricing or configure Shopify Billing/App Pricing before any paid listing.
- [ ] Enter factual listing copy without unsupported claims.
- [ ] Run Shopify App Store automated checks from the authorized dashboard session.
- [ ] Press final **Submit for Review**.

## Close criteria

Close this release handoff only after issue #211 records evidence that the target
issue EVNSolution/shopify-clever#6 has received dashboard completion proof and the
app has been submitted for Shopify App Store review.
