# 📸 Ultimate Screenshot — capture any web page as an image, PDF, video or GIF

**[Ultimate Screenshot](https://apify.com/dz_omar/ultimate-screenshot?fpr=smcx63)** turns a list of web addresses into finished files: JPEG and PNG screenshots, printable PDFs, and short MP4 or GIF recordings of the page. It drives a real browser, so pages that need JavaScript, web fonts, lazy-loaded images or a signed-in session come out looking the way a person would see them — on a desktop window, or on any of **131 phone and tablet presets**.

[![Ultimate Screenshot](https://raw.githubusercontent.com/FlowExtractAPI/Ultimate-Screenshot/refs/heads/main/Ultimate%20Screenshot.png)](https://apify.com/dz_omar/ultimate-screenshot?fpr=smcx63)

Perfect for **agencies** archiving client sites, **QA and product teams** building visual checks, and **marketers** who need a scroll-through clip of a landing page without opening a screen recorder.

---

## What it produces

| Format | What you get | Typical use |
|---|---|---|
| **JPEG** | Compressed image, quality you choose | Bulk archiving, thumbnails, previews |
| **PNG** | Lossless image | Design review, pixel comparisons |
| **PDF** | Printable document — paper size, margins, orientation | Reports, invoices, compliance records |
| **MP4** | Up to a 30-second recording | Landing-page walkthroughs, demos |
| **GIF** | Up to a 10-second looping animation | Social posts, docs, changelog entries |

Every format can be captured **as the visible window** or **as the whole page**, top to bottom.

---

## ⚙️ How to use it

### The one setting you need

```json
{
    "linkUrls": ["https://example.com", "https://apify.com"]
}
```

That captures each page as a JPEG at 1920 × 1080. Everything below is optional.

Addresses go in one per line. A bare domain is fine — `example.com` becomes `https://example.com`. The field checks that each line looks like a web address, so a stray number or word is caught in the form rather than producing an empty result.

### Full-page screenshot

```json
{
    "linkUrls": ["https://example.com"],
    "outputFormat": "png",
    "fullPage": true,
    "scrollToBottom": true,
    "maxScrollSeconds": 15
}
```

`scrollToBottom` walks down the page first so lazy-loaded images actually load. `maxScrollSeconds` is the limit that ends the scroll — on an endless feed it is the *only* thing that ends it, and it decides how much of the feed you capture.

### Phone or tablet

```json
{
    "linkUrls": ["https://example.com"],
    "device": "iPhone 15 Pro",
    "outputFormat": "jpeg",
    "fullPage": true
}
```

131 presets, each with the right screen size, pixel density and browser identity. A preset replaces the window width and height below it.

### A scroll-through video

```json
{
    "linkUrls": ["https://example.com"],
    "outputFormat": "mp4",
    "recordingMode": "scroll",
    "recordSeconds": 10,
    "fps": 15
}
```

The clip is exactly `recordSeconds × fps` frames and plays for exactly `recordSeconds`. In **scroll** mode the step between frames is worked out from the real page height, so the last frame lands at the bottom of the page. In **static** mode the window stays put, which suits carousels, animations and players.

### A signed-in page

```json
{
    "linkUrls": ["https://example.com/account"],
    "cookies": [
        { "name": "session_id", "value": "abc123", "domain": "example.com" }
    ]
}
```

Export cookies with a browser extension such as [Cookie Editor](https://cookie-editor.com/). Each cookie needs at least a name and a value; the address being captured supplies the domain when it is missing.

### Cleaning up the page first

```json
{
    "linkUrls": ["https://example.com"],
    "waitForSelector": "#main-content",
    "hideSelectors": ["#cookie-banner", ".chat-widget", ".sticky-header"],
    "delayBeforeScreenshot": 2000
}
```

**Finding the right selector.** Right-click the thing you want gone → *Inspect* → find the outermost box that wraps the whole banner → use its `id` as `#the-id`, or a class as `.the-class`. Hide the wrapper, not the text inside it, or you will be left with an empty bar.

Most consent banners come from a handful of platforms, so these cover a lot of the web:

| Platform | Selector |
|---|---|
| OneTrust (used by apify.com and many large sites) | `#onetrust-consent-sdk` |
| Cookiebot | `#CybotCookiebotDialog` |
| Osano | `.osano-cm-window` |
| Quantcast / TCF | `.qc-cmp2-container` |
| Usercentrics | `#usercentrics-root` |
| CookieYes | `.cky-consent-container` |
| Generic catch-all | `[id*="cookie"]`, `[class*="cookie-banner"]` |

Hiding is applied as a style rule as soon as the page opens, so a banner that appears **after** the page has loaded is caught too — many consent scripts take several seconds to inject theirs.

If a selector matches nothing, the result row's `notes` says so rather than leaving you guessing.

---

## 🎛️ All settings

### Page loading

| Setting | Default | What it does |
|---|---|---|
| `waitUntil` | `load` | When the page counts as ready. `load` suits most sites and is much the fastest complete option. Move to a Network Idle mode only for pages that keep drawing after everything has loaded — it can add many seconds per page. |
| `timeouT` | `30` s | How long to wait for a page before giving up on that attempt. |
| `maxRetries` | `2` | Extra attempts when a page fails to load. `0` means a single attempt. |
| `delayBeforeScreenshot` | `1000` ms | Extra pause after the page is ready, for animations and fonts. |
| `waitForSelector` | — | CSS selector to wait for. If it never appears the page is captured anyway and the row says so. |

### Scrolling & clean-up

| Setting | Default | What it does |
|---|---|---|
| `scrollToBottom` | `false` | Scroll through the page before capturing so lazy content loads. |
| `maxScrollSeconds` | `15` s | The longest scrolling may take. Scrolling also stops as soon as the page stops growing. |
| `delayAfterScrolling` | `500` ms | Pause after scrolling, so content loaded on the way down can render. |
| `hideSelectors` | — | CSS selectors to hide — cookie banners, chat bubbles, sticky headers. Applied as a style rule the moment the page opens, so it also catches banners that appear seconds later. Selectors matching nothing are reported in `notes`. |

### Video & GIF

| Setting | Default | What it does |
|---|---|---|
| `recordingMode` | `scroll` | `scroll` pans down the page; `static` holds the window still. |
| `recordSeconds` | `5` s | How long the finished clip plays. GIF max 10, MP4 max 30. |
| `fps` | `10` | Playback frame rate. GIF max 15, MP4 max 30. |

### Image, PDF, device and network

| Setting | Default | What it does |
|---|---|---|
| `jpegQuality` | `85` | JPEG only. PNG is always lossless. |
| `printBackground` | `true` | PDF: keep background colours and images. |
| `formaT` | `A4` | PDF paper size. Ignored when any margin below is above 0. |
| `landscape` | `false` | PDF orientation. |
| `toP` / `righT` / `bottoM` / `lefT` | `0` mm | PDF margins in **millimetres**. |
| `device` | — | One of 131 phone/tablet presets. Replaces the window size. |
| `window_Width` / `window_Height` | `1920` × `1080` | Browser window when no device preset is chosen. |
| `userAgent` | — | Send a specific browser identity. |
| `cookies` | `[]` | Cookies applied before the page loads. |
| `proxyConfig` | Apify datacenter | Route the capture through a proxy. |
| `enableSSL` | `true` | Turn off only for a self-signed or expired certificate you trust. |

---

## 📊 What you get back

One row per address, plus the file itself in the run's storage.

**Files are grouped by what they are.** Open the run's Storage tab and you get three named collections rather than one flat list:

| Collection | Holds | Key prefix |
|---|---|---|
| **Screenshots** | PNG and JPEG images | `screenshot-` |
| **Videos & GIFs** | MP4 and GIF recordings | `video-` |
| **PDF documents** | PDF renderings | `document-` |

A GIF is filed with video, not with the images — it is a *recording* of the page, made by the same frame capture and described by the same length and frame-rate settings as an MP4. Its file type happens to be `image/gif`, which is why the video collection accepts that type too.

```json
{
    "status": "success",
    "format": "jpeg",
    "linkUrl": "https://example.com",
    "finalUrl": "https://example.com/",
    "screenshot_url": "https://api.apify.com/v2/key-value-stores/.../records/capture-001-....jpg",
    "screenshot_image": "https://api.apify.com/v2/key-value-stores/.../records/capture-001-....jpg",
    "content_Type": "image/jpeg",
    "fileName": "capture-001-20260904211602-nyyjjkaa.jpg",
    "fileSizeBytes": 24576,
    "pageTitle": "Example Domain",
    "httpStatus": 200,
    "width": 1920,
    "height": 1080,
    "durationSeconds": null,
    "frames": null,
    "captureMs": 2388,
    "error": null,
    "notes": null,
    "capturedAt": "2026-09-04T21:16:02.463Z"
}
```

| Field | What it tells you |
|---|---|
| `status` | `success` when a file was produced, `failed` when the page returned nothing |
| `screenshot_url` / `screenshot_image` | Direct download link for the file |
| `content_Type`, `fileName`, `fileSizeBytes` | What the file is and how big |
| `linkUrl` / `finalUrl` | What you asked for, and where the page ended up after redirects |
| `pageTitle`, `httpStatus` | The page's own title, and the status code it answered with |
| `frames`, `durationSeconds` | Recordings only — how many frames and how long the clip plays |
| `captureMs` | How long this page took, from opening it to saving the file |
| `error` | On a failed page, one sentence saying what happened |
| `notes` | Where the actor tells you it did something you did not ask for |

`screenshot_image`, `content_Type`, `linkUrl` and `screenshot_url` keep the exact names and meanings they had in version 1 — existing integrations need no change.

**`notes` is worth reading.** It is how the actor reports a clipped tall page, a scroll that stopped at the limit, a recording shortened to stay inside the frame limit, or `hideSelectors` that matched nothing.

---

## 💰 Pricing

**$13 / month**, with a **1-day free trial**. Unlimited runs and unlimited captures for the subscription; you pay the Apify platform usage your runs consume, as with any rented Actor.

The settings that move platform usage most:

- **`waitUntil`** is the big one. `load` returned a heavy marketing page in about **5 seconds** where `networkidle0` took **28** — for a screenshot that differed by a few kilobytes.
- **Capture in batches.** Starting a run costs a fixed amount of browser time before the first page opens. Ten addresses in one run use roughly half the platform usage of the same ten as ten separate runs.
- **Recordings cost far more than stills**, in proportion to `recordSeconds × fps`. A 5-second GIF at 10 fps is 50 captures of the page; a 10-second MP4 at 30 fps is 300.

<!-- ══════════════════════════════════════════════════════════════════════════
▼▼▼ PAY-PER-EVENT PRICING — PARKED UNTIL THE LISTING IS SWITCHED TO PPE ▼▼▼

This actor is currently on the monthly rental plan above. The table below is the
prepared per-event pricing; it is hidden so that nobody reads prices they are not
being charged.

TO PUBLISH IT, once the listing has been switched to pay-per-event:
  1. delete this comment opener and the closer marked ▲▲▲ below
  2. delete the "$13 / month" paragraph above
  3. un-comment the billing block in src/delivery.js (same ▼▼▼ / ▲▲▲ banner)
  4. rename "_payPerEvent_DISABLED_UNTIL_PPE" back to "payPerEvent" in
     .actor/actor.json
The numbers below match .actor/pay_per_event.json exactly — change both together.

### Pricing

| Event | FREE | BRONZE | SILVER | GOLD |
|---|---|---|---|---|
| **Run start** (once per run) | $0.002 | $0.0015 | $0.001 | $0.001 |
| **Browser time** (per GB of memory, per 30 s) | $0.005 | $0.0042 | $0.0042 | $0.0042 |
| **Page capture** (per saved file) | $0.007 | $0.005 | $0.004 | $0.0035 |

**What a run actually costs** — measured on this actor at 2 GB:

| Run | Browser time | Total on BRONZE |
|---|---|---|
| One JPEG of a normal page | ~1 unit | ~$0.011 |
| Ten JPEGs in a single run | ~2 units | ~$0.060 (≈ $0.006 per page) |
| One PDF | ~1 unit | ~$0.011 |
| A 5-second GIF at 10 fps | ~3 units | ~$0.019 |
| A 10-second MP4 at 15 fps | ~8 units | ~$0.040 |

Pages that produce no file are never charged — they still appear in your results
with a sentence explaining what happened.

▲▲▲ END OF THE PARKED PAY-PER-EVENT SECTION ▲▲▲
══════════════════════════════════════════════════════════════════════════ -->

---

## 🛟 Limits, and why they exist

A browser can be asked to do something that never finishes. These limits mean it does not.

| Limit | Value | What happens when it is reached |
|---|---|---|
| Scrolling | your `maxScrollSeconds`, at most 60 s | Scrolling stops, the page is captured as it stands, and a note says so |
| Full-page image height | 16 000 px | The image is clipped to the top 16 000 px — the tallest a browser can render in one pass |
| Recording frames | 300 per page | The clip is shortened, keeping your frame rate, and a note says so |
| Recording time | 2.5 minutes per page | Recording stops and whatever was captured is encoded into a valid clip |
| Whole page | 4 minutes | The page is abandoned and reported; the run carries on with the next address |
| Output file | 100 MB | The page is reported with a suggestion to record less or use a smaller window |

---

## 🔄 If a run is interrupted

Progress is saved after every page. If the run is migrated, restarted or aborted, it resumes from the next uncaptured address instead of starting over — so an interruption never re-captures a page you already received.

---

## 🚫 When something goes wrong

| Situation | What you see | What to do |
|---|---|---|
| An address is not a web address | The run is refused when you submit it, naming the position of the bad line | Fix or remove that line |
| A page will not load | `status: "failed"` and an `error` sentence for that address only | Raise `timeouT`, or check the address in a browser |
| The site answers with an error code | A capture of the error page, and a note giving the status code | Expected — the page really did answer that way |
| A page is taller than 16 000 px | A clipped image, and a note with the page's real height | Capture the window instead of the whole page, or a narrower window |
| A recording came out short | Fewer `frames` than asked, and a note saying why | Lower `fps` or `recordSeconds`, or record a lighter page |
| Nothing was captured at all | A single row with `status: "no_input"` | Check the addresses in the input |

---

## ❓ Frequently asked questions

**Can it capture pages behind a login?**
Yes, with the `cookies` setting. Export the cookies from a signed-in browser session and paste them in.

**Why is my full-page screenshot cut off at the bottom?**
The page is taller than 16 000 pixels. That is a browser limit, not a setting — a browser cannot rasterise an arbitrarily tall page in one image. The row's `notes` gives the page's real height.

**My GIF looks shorter than I asked for.**
Check `frames` and `notes` on the row. A clip is shortened when `recordSeconds × fps` would pass 300 frames, or when the page was too slow to record the full series in time.

**Why is the video not the page's real motion?**
Frames are captured one at a time, so a recording is a time-lapse rather than a real-time screen capture. In `scroll` mode that is exactly what you want — a smooth pass down the page. In `static` mode, fast animations look sped up.

**Can I capture an infinite-scroll feed completely?**
No, and neither can anything else — the feed has no end. Set `maxScrollSeconds` to how much of it you want; that is the amount you get.

**How many pages can I capture per run?**
As many addresses as you like. Batching is more efficient than separate runs.

**What happens if one address fails?**
Only that address. It appears with `status: "failed"` and a sentence saying why, and the run continues.

**I upgraded from version 1 — do I need to change anything?**
No. Every setting from version 1 still works, and the four output fields integrations read keep their exact names. The retired video settings (`frameCounT`, `frame`, `timefullPagE`, `infiniteScroll`) are converted automatically and the run log says what they became. See the changelog for the full list.

**Can I use my own proxy?**
Yes — set your own proxy URLs in the proxy setting. Apify's datacenter proxy is used by default.

---

## ⚖️ Legal & compliance

This actor captures **pages as they are served to a browser** — the same content any visitor can see, plus anything your own cookies give you access to.

Please use it responsibly:

- Only capture pages you are authorised to access, and only use cookies from accounts you own.
- Respect each site's Terms of Service and applicable data-protection law (GDPR, CCPA and equivalents).
- Screenshots of pages containing personal data are personal data — store and share them accordingly.
- Do not use captures to impersonate a site, a person or an organisation.

---

## 🤝 Support

- **📧 Email**: [flowextractapi@outlook.com](mailto:flowextractapi@outlook.com)
- **🐙 GitHub**: [FlowExtractAPI](https://github.com/FlowExtractAPI)
- **🐦 Twitter**: [@FlowExtractAPI](https://x.com/FlowExtractAPI)
- **🔧 Apify**: [FlowExtract API](https://apify.com/dz_omar?fpr=smcx63)


---

*Built by [FlowExtract API](https://apify.com/dz_omar?fpr=smcx63) — reliable data extraction actors for the Apify platform.*
