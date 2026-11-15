# Notifier for Gmail™ (patched)

Unofficial patched copy of [Notifier for Gmail™](https://chromewebstore.google.com/detail/notifier-for-gmail/dcjichoefijpinlfnjghokpkojhlhkgl) v1.2.5 by InBasic ([homepage](https://webextension.org/listing/gmail-notifier.html)), licensed under MPL 1.1 (see `LICENSE`).

## Fix

"Mark as Read" (and the other actions) stopped working: the email vanished from the popup but stayed unread in Gmail.

The extension reads Gmail's `GMAIL_AT` token cookie with `chrome.cookies.get` on `https://mail.google.com/mail/u/N`. Gmail now sets that cookie on `/mail/u/N/` and `/mail/u/N/s/`, so the lookup found nothing, and the fallback (the retired Basic HTML view) fails too. Actions are posted to `/mail/u/N/s/`, so the token for that path is the one Gmail expects.

The fix is one line in `worker.js`:

```diff
- chrome.cookies.get({name: 'GMAIL_AT', url: 'https://mail.google.com/mail/u/' + request.n}, o => {
+ chrome.cookies.get({name: 'GMAIL_AT', url: 'https://mail.google.com/mail/u/' + request.n + '/s/'}, o => {
```

`manifest.json` also drops `key` and `update_url` so this copy gets its own ID and is never overwritten by the store, and `_metadata/` (store signatures) is removed.

## Install

1. Download this repo (Code → Download ZIP) and unzip it.
2. Open `chrome://extensions`, disable or remove the store version, and turn on **Developer mode**.
3. Click **Load unpacked** and select the unzipped folder.
4. Reconfigure the extension's options; it starts with default settings.

It does not update automatically.
