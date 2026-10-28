---
title: ChatterBox AI - Privacy Policy
---

# Privacy Policy — ChatterBox AI

**Last updated:** 28 October 2026
**Applies to:** the ChatterBox AI mobile, desktop and web application (the "app").


---

## The short version

- The app has **no account, no server of ours, no analytics and no advertising**.
- The messages you type are sent **directly to the AI provider you configure**
  (River AI by default, or your own proxy). That provider processes them under
  its own terms.
- Everything else — your API key, your saved chats, your settings — stays **on
  your device**.
- You can delete everything from the app's Settings, or by uninstalling.

---

## 1. Who is responsible, and how to contact us

The app is published by an independent developer ("we"). There is no company
and no data-processing agreement: you are the only party that decides which AI
provider the app talks to.

**Privacy contact:** `privacy@YOUR-DOMAIN` — replace with the address you will
actually monitor.

If you do not own a domain yet (common, and not required), a dedicated mailbox
such as `chatterboxai.privacy@gmail.com` is perfectly acceptable and is what
most independent apps use. Use a **dedicated** address, not your personal one:
this address will be published on the store listing and will receive spam.

We answer privacy requests within 30 days.

## 2. What leaves your device

| Data | Where it goes | Why |
|---|---|---|
| The text of each message you send | The AI provider whose **base URL** is configured in Settings | To generate an answer |
| The conversation history included in the request (previous turns) | Same | The model needs context to answer a follow-up |
| The selected model id | Same | So the provider routes to the right model |
| Your API key (or the proxy token, when a proxy is configured) | Same, as an `Authorization` header | To authenticate you |

That is the complete list for the default build: it talks to the provider
directly, and no server we operate is involved.

**If your build is configured with the optional proxy** (the Worker in
`server/`), the same data goes to that proxy first and is forwarded unchanged to
the provider. The proxy does not store, log or inspect message content, keeps
the provider key server-side, and enforces a per-IP request limit. It is a
pass-through: nothing is retained after the response has been streamed back.

**Default provider:** River AI (`https://api.river.ai/v1`). Its handling of your
messages is governed by River's own privacy policy and terms, which you should
read before using the app. If you change the base URL to your own backend
proxy, that proxy's policy applies instead — and, if you use a proxy, the
provider never sees your key.

## 3. What stays on your device

| Data | Where it is stored | Encrypted at rest? |
|---|---|---|
| API key | Platform secure storage — Android Keystore-backed, iOS Keychain. On the web: page memory only, never browser storage | **Yes**, by the OS on mobile/desktop; **no** on the web (session-only) |
| Saved conversations (titles, message text, model reasoning text, error text) | Private app preferences (Android SharedPreferences / iOS NSUserDefaults) on native; browser `localStorage` on the web — ciphertext envelopes, never message plaintext | **Yes** — AES-256-GCM on native and on web when passphrase encryption is enabled. Session-only web mode writes nothing |
| Selected model, base URL | Private app preferences | No |
| Theme choice | Private app preferences | No |
| Language choice | Private app preferences | No |
| Onboarding completed, privacy notice accepted | Private app preferences | No |
| Daily-reminder switch and time | Private app preferences | No |

Two consequences worth stating plainly:

- **The conversation history is encrypted at rest.** Native builds store each
  thread as an AES-256-GCM envelope whose key lives in platform secure storage.
  Web builds encrypt with a key derived from a passphrase that is never stored,
  or write nothing at all in session-only mode. The app sets
  `android:allowBackup="false"`, so it is not copied off the device by Android
  cloud or ADB backup. On Android 12+ the app also ships
  `data_extraction_rules.xml`, which excludes app preferences, databases and
  files from **both** cloud backup and device-to-device transfer. iOS does not
  back it up beyond the normal device backup. Encryption does not protect data
  on a device while the app is unlocked or compromised, and losing both the
  encryption key and the recovery code makes the saved history unreadable.
- **The API key survives an iOS uninstall.** Keychain items are not removed when
  an app is deleted. Use **Settings → Remove key** before uninstalling if that
  matters to you.
- **If the OS keychain is unavailable**, the app cannot encrypt the key at rest.
  In that case the key may remain in private app preferences for that
  build/session (including a plaintext copy kept for migration) so it is not
  lost, and the app reports that it could not persist the key. The key is still
  sent only to the configured base URL.

**On the web build** there is no OS-protected key store. On first use the app
asks you to set a passphrase, derives a key from it with PBKDF2, and uses that
key only in page memory; the passphrase-wrapped conversation key, the ciphertext,
and (once configured) the wrapped recovery envelope and a skip marker are written
to `localStorage`. You may instead choose session-only mode, which writes no
conversation data at all, though unrelated settings such as theme and language
may still be stored. Reopening the tab requires the passphrase, a one-time
recovery code, or a destructive reset. Settings and the API key follow the same
browser-storage rules as before. A script running in the
page — including an XSS payload — can read in-memory plaintext/key material while
the app is unlocked, but the data written to `localStorage` is ciphertext, not
message text. On Android and iOS the key is held in the OS-protected key store
(Keystore / Keychain) and local data is app-private.

## 4. What we do not collect

No analytics SDK. No advertising SDK. No crash-reporting SDK. No account, email
address or phone number. No location, contacts, calendar, photos, microphone or
camera access. No advertising identifier, no device fingerprint, no
cross-app tracking. The app requests only these Android permissions:

| Permission | Why |
|---|---|
| `INTERNET` | To reach the AI provider |
| `POST_NOTIFICATIONS` | To show the optional daily reminder (Android 13+) |
| `VIBRATE` | The reminder's notification vibration |
| `RECEIVE_BOOT_COMPLETED` | To reschedule the reminder after a reboot |

The app is not built with `SCHEDULE_EXACT_ALARM` and uses inexact scheduling, so
it never asks to bypass battery optimisations.

## 5. Third parties

The only third party involved is **the AI provider you configure**. There is no
data broker, no sub-processor and no shared analytics. If you use a model
provider other than River by entering a different base URL, that provider
becomes the third party.

## 6. Retention

- **On your device:** until you delete it (see §7). Nothing expires on its own.
  The app never removes an old conversation to make room for a new one: at 100
  saved threads, creating another is refused until you delete one.
- **At the provider:** determined by that provider's policy and your account
  with them. We cannot delete data held by a provider on your behalf.

## 7. Deleting your data

| What | How |
|---|---|
| One conversation | Chats screen → the delete icon on that row |
| All conversations | Settings → Clear conversation |
| The API key | Settings → Remove key. This deletes the saved key, removes the old plaintext copy and records the removal so the next launch cannot re-import it. If the OS store cannot confirm the deletion, the app does not claim a confirmed wipe. In a build that embeds a key with `--dart-define`, removing the saved key falls back to that embedded key — it is not a full credential wipe in that build |
| The daily reminder | Settings → turn the reminder off (cancels the scheduled notification) |
| Everything | Uninstall the app, after removing the key on iOS |

All deletion is immediate and local. There is no server-side copy to request.

## 8. Your rights

Because the app holds your data only on your device, you exercise every right —
access, correction, deletion, portability — directly through the app, without
contacting anyone. Under the GDPR and similar regimes you also have the right
to lodge a complaint with your supervisory authority.

## 9. Children

The app is not directed at children under 13 (or the minimum age in your
country), and we do not knowingly process their data. If a child has used the
app, delete the conversations and the key as described in §7.

## 10. Security measures — and their limits

What the app does:

- Keeps the API key in platform secure storage on mobile and desktop, never in
  the source code and normally not in plaintext preferences. On the web the key
  is kept in page memory only, never in browser storage — but that does not
  protect it from cross-site scripting. If the OS keychain is unavailable, the
  key stays in private preferences for that build/session and is used from
  memory; the app reports that it could not persist the key.
- Encrypts saved conversations with AES-256-GCM. Native keys live in platform
  secure storage; web keys are derived from a passphrase and exist only in page
  memory, with the wrapped key and ciphertext in `localStorage`. A one-time
  recovery code can restore a lost native key. Message plaintext is not written
  to the system log.
- Accepts HTTPS base URLs only. `http://` is rejected in Settings and on every
  read of a stored, legacy or build-time value, with one documented development
  exception: the loopback hosts `localhost` and `127.0.0.1` may use plain HTTP
  for a local proxy or model daemon. Any other configured host must use HTTPS,
  so provider traffic is HTTPS for every user-entered or stored URL. Android
  also ships `usesCleartextTraffic="false"` plus a network-security config, and
  iOS has no App Transport Security exception; those OS rules may additionally
  block loopback HTTP.
- Disables Android backup, and on Android 12+ ships `data_extraction_rules.xml`
  excluding app preferences, databases and files from both cloud backup and
  device-to-device transfer, so local data cannot be extracted through ADB,
  cloud backup or a device transfer.
- Disables Android debugging in release builds and declares no exported
  components other than the launcher activity. R8 shrinking/obfuscation is
  currently **disabled**: enabling it broke a Pigeon-based plugin at startup on
  a real Android 14 device, so the release build is not obfuscated.
- Writes no message content to the system log.

What it does **not** do: it does not protect data on a compromised or unlocked
device, and it cannot protect in-memory plaintext from script running in the
page while the web app is unlocked. It also cannot recover history if both the
encryption key and the recovery code are lost.

## 11. International transfers

The AI provider may process your messages in a country other than yours. That
transfer is governed by the provider's own terms and safeguards, not by this
policy.

## 12. Changes to this policy

Material changes will be published at the URL in §14 with a new "last updated"
date. Continuing to use the app after a change means you accept the updated
policy.

## 13. Store declarations — how to answer the questionnaires

Your answers must match §2–§4. This is the mapping:

**Google Play → Data safety**

| Question | Answer |
|---|---|
| Does your app collect or share any required user data types? | **Yes** — "Messages / in-app messages" |
| Is it collected or shared? | **Shared** (sent to the provider); not collected by us |
| Is it processed ephemerally? | **No** — the provider may retain it |
| Is it required or optional? | **Required** (the app cannot answer without it) |
| Is it encrypted in transit? | **Yes** |
| Can users request deletion? | **Yes** — in-app (§7); provider-side per their policy |
| Location, personal info, financial, health, contacts, photos, files, calendar, app activity, device ids | **None** |
| App is a "news" or "COVID" app? | No |

**Apple App Store → App Privacy**

| Category | Answer |
|---|---|
| Data used to track you | **None** |
| Data linked to you | **None** |
| Data not linked to you | **User content → Other user content** (messages sent to the provider), purpose: *App Functionality* |
| Analytics / Advertising / Diagnostics | **Not collected** |

Both stores also require the privacy-policy **URL** — §14.

## 14. Publishing checklist

1. Replace the contact line in §1 with an address you monitor.
2. Host this file so it has a public URL. With no domain, the free option is
   GitHub Pages: put this file in `docs/privacy.md` of the repository, enable
   *Settings → Pages → Deploy from branch → /docs*, and the URL becomes
   `https://<username>.github.io/<repo>/privacy.html`.
3. Paste that URL into the Play Console listing and App Store Connect.
4. Answer the store questionnaires using §13 verbatim.
5. If you add analytics, crash reporting, an in-app purchase or a backend proxy
   that logs requests, **update §2, §4 and §13 in the same release** — an
   out-of-date Data Safety declaration is itself a policy violation.