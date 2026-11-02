# Read-Along

An offline iPhone reader for read-along books: the original printed pages, with the sentence being read highlighted, at any speed from 0.5x to 3x.

**Open it:** https://dylanw1015-collab.github.io/read-along/

The books come from [pdf-audio](https://github.com/dylanw1015-collab/pdf-audio). That tool turns a PDF into a read-along EPUB on the laptop (`pdfaudiobook export book.pdf --format epub`, or *Export for iPhone…* in its window). This app plays those EPUBs, and any other EPUB 3 with Media Overlays.

## Use it on an iPhone, offline

1. Open the link in Safari, tap Share, then tap **Add to Home Screen**.
2. Open **Read-Along** from the Home Screen. Tap **Add a book**, then pick the `.epub` in Files (iCloud Drive, or AirDrop it to yourself first).
3. Pick a book, then tap play. Slide **Speed** to change the speed. To read from a sentence, tap it. Pinch to zoom.

After a book has been added, the app needs no internet, so it works in airplane mode. Add books from inside the Home Screen app, because iOS gives it separate storage from Safari.

## Why it is built this way

| Choice | Why / evidence |
|---|---|
| A web app, not an App Store app | It's free, and works on an iPhone without a Mac or a $99/yr developer account. Once added to the Home Screen, a web app gets its own offline storage ([WebKit: storage policy](https://webkit.org/blog/14403/updates-to-storage-policy/)). |
| A speed slider using `playbackRate` | Apple Books plays Media Overlays but has no speed setting for them. An HTML `<audio>` element plays at any rate, and `preservesPitch` (true by default) keeps the voice from sounding higher ([HTML spec](https://html.spec.whatwg.org/multipage/media.html#dom-media-preservespitch)). |
| Plays EPUB 3 Media Overlays | Each SMIL `<par>` pairs a sentence on a page with an audio clip ([EPUB 3.3, Media Overlays](https://www.w3.org/TR/epub-33/#sec-media-overlays)). The sentence whose clip contains the current audio time gets the book's own highlight class, so the highlight looks the same as in Books. |
| Unzipped once into IndexedDB | The book opens later without the network. Only the pages near the screen hold images in memory, which matters for books of hundreds of MB on a phone. |
| A service worker caches the app | The app's own files load in airplane mode ([MDN: Service Worker API](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)). |
| Each page drawn in its own shadow root | The book's stylesheet styles only that page, never the app. |
| JSZip 3.10.1, bundled locally | Reads the .epub zip in the browser, with nothing loaded from a CDN, so it works offline. MIT license. |

## Tested

Tested in headless Chromium at iPhone size (390×844) with a 27 MB, 28-page book:
- The import took 2.7 s.
- At 2x, playback moved across audio files and pages, and the highlight stayed on the sentence being read.
- Position and speed were still set after a reload.

**Not yet tested on a real iPhone.** These parts still need checking there:
- that it works with no network (airplane mode);
- that audio keeps playing with the screen locked;
- that the lock-screen controls work.
