# Soft60 acquisition measurement

## What is recorded
The public Soft60.app and ShaniaKhan.com pages use an anonymous PostHog marketing tracker with:
- `marketing_landing_viewed`: `site`, `page`, allowlisted `utm_*` campaign labels and referring **domain** only.
- `marketing_cta_clicked`: `site`, `page`, `cta_kind` and `destination`, plus allowlisted campaign labels.

Both sites use the existing Soft60 PostHog project. Marketing sessions are memory-only; session recordings and autocapture are disabled; no full URL query strings, user emails, form contents or health information are intentionally sent. The tracker honors browser GPC/DNT signals.

## Funnel interpretation
1. Marketing landing visit: measured by `marketing_landing_viewed`.
2. Marketing intent: measured by `marketing_cta_clicked` with `app_download`, `assessment`, or `app_discovery`. This is *not* an actual installation or assessment completion.
3. In-app experience: existing `app_open`, `paywall_viewed`, `subscription_started`, and `subscription_completed` events.
4. Authoritative trial/paid memberships: RevenueCat and Stripe/DB billing status, not `subscription_completed` client-side proxy. Keep this as a **separate verified source** until billing data is connected to analytics.

## Attribution limits
Websites use memory-only anonymous IDs (do not persist a cross-site identity). A user who taps an App Store or Google Play link cannot automatically be joined back to a later app install or paid subscription just because both visits are in PostHog. UTM source is preserved for the founder-site → Soft60 site route and the Soft60 site → Frequency Assessment route. On the app website, first-touch UTM parameters are captured before onboarding navigation and are included on subsequent app-open events for identified visitors.

## First reporting view
In PostHog, inspect:
- Marketing visits by `site` and `utm_source`.
- App-store/Google Play and assessment CTA clicks by `site`, `destination`, and `cta_kind`.
- App opens by `first_utm_source` (eligible only when the app saw a tagged campaign link).
- Paywall/checkout intent separately.
- Verified trial starts, active paid members, and recognized revenue from billing, after a reconciled billing integration.

Do not divide anonymous CTA clicks by paid subscription events as though those are a matched user cohort. Never claim a campaign produced paid subscriptions without a supported attribution path.

## Quality assurance
- Visit each site with a test UTM such as `?utm_source=instagram&utm_medium=social&utm_campaign=soft60_launch`.
- In PostHog, confirm one `marketing_landing_viewed` plus a `marketing_cta_clicked` for an actual download or assessment link.
- Check events have no emails, full query strings, sensitive health data, or journal text.
- Confirm GPC and DNT disable the public-site script.
- Verify no analytics failure blocks navigation.
- Compare site clicks with the App Store Connect and Google Play Console referral reports (when available), and reconcile trials/revenue against billing.
