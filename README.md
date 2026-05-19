# webxdc-apps

A tiny, hand-curated app picker for [webxdc](https://webxdc.org) apps inside
[Delta Chat](https://delta.chat).

## What this is

Delta Chat lets you point its in-app "Apps" picker at any URL you like
(see [deltachat-desktop#6215](https://github.com/deltachat/deltachat-desktop/pull/6215)
and [deltachat-ios#3082](https://github.com/deltachat/deltachat-ios/pull/3082)).
This repo is one such URL: a single `index.html` that lists a handful of apps
as tap-to-install cards.

The default picker at `webxdc.org/apps/` lists hundreds of apps. This one is
deliberately tiny, picked by hand, no search bar, no
categories, no telemetry.

How did I decide on which apps to include? I'm only including ones that fulfill an actual need. Games? Only multiplayer ones with a real social / connective component. Tools? Only ones that fulfill a real use-case for group chats. Unfortunately, 99% of webxdc apps are a solution seeking a problem.

## How it works

Each card is a plain `<a href="…file.xdc">` pointing at a GitHub release asset.
When the page is loaded inside Delta Chat's picker, tapping a card triggers the
`.xdc` download, which Delta Chat intercepts and adds to the current chat.

There's no SDK, no JavaScript bridge, no manifest file. Just links.

Download URLs are pinned to a specific release tag. e.g.
`/releases/download/0.3.0/app.xdc`, not `/releases/latest/download/app.xdc`.
Users see the exact version in the details dialog before they tap install,
which makes the download verifiable. The trade-off is that the maintainer
has to bump the URL (and the displayed `version`) when a new release ships.

## Adding an app

Edit the `APPS` array near the bottom of [`index.html`](./index.html):

```js
{
  name: "App Name",
  author: "github-username",
  icon: "data:image/png;base64,…",       // inline the icon as a data URI
  short: "One-line teaser for the list.",
  description: "Longer paragraph(s) for the details dialog.",
  version: "1.0.0",                      // shown in the details dialog
  size: "~30 KB",                        // approximate, hand-maintained
  source: "https://github.com/owner/repo",
  url: "https://github.com/owner/repo/releases/download/1.0.0/app.xdc"  // pinned to a tag
}
```

When you bump to a new release, update **three** things in the entry: the
tag in `url`, the `version` string, and (if needed) the `size`.

## Privacy

No analytics, no fonts, no CDN, no trackers, no `localStorage`, no cookies.

**On page load**, the page makes zero third-party requests. Everything needed
to render the list, including icons (inlined as base64 data URIs) and
approximate app sizes (hard-coded in the `APPS` array) — ships inside the
single `index.html`.

**On tap of a card**, no network requests fire. The details dialog opens
instantly using only local data.

**On tap of "Add to chat"**, the `.xdc` file downloads from GitHub release
storage. Delta Chat intercepts the download and adds the app to the chat.

**On tap of "View source code"**, the user is sent to the repo on GitHub.

Sizes shown in the details dialog are approximate values written by hand in
the `APPS` array; bump them when you publish a release that materially
changes the file size.

## Hosting

It's a static `index.html`. Serve it from anywhere. Point Delta Chat's app picker URL setting at it.
