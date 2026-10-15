# TextLooker 👀

A tiny, single-file texthooker page. It connects to Textractor's WebSocket extension and shows the hooked text as plain, selectable lines so pop-up dictionaries like [Yomitan](https://github.com/yomidevs/yomitan) can scan it.

No build step, no dependencies, no server of its own: just one `index.html` file.

**Live version:** `https://YOUR-USERNAME.github.io/REPO-NAME/`

## Features

- Connects to a Textractor WebSocket server (default `ws://127.0.0.1:6677`)
- Text is rendered as ordinary DOM text, so Yomitan can scan it
- One socket at a time, with automatic reconnect and backoff (1s up to 10s), so it doesn't hammer a fragile server
- Rewrites `localhost` to `127.0.0.1` to avoid IPv6 resolution problems (for example under Wine)
- Optional "Skip repeats" to ignore consecutive duplicate lines
- Strips control and zero-width characters that can break word scanning
- Adjustable text size, light/dark theme, clear button
- Remembers your settings between sessions
- Keeps the last 500 lines
- "Test line" button to check that Yomitan works on the page

## Requirements

- [Textractor](https://github.com/Artikash/Textractor) with a WebSocket extension loaded, such as [textractor_websocket](https://github.com/kuroahna/textractor_websocket)
- A modern browser
- [Yomitan](https://github.com/yomidevs/yomitan) installed in that browser

## Usage

1. In Textractor, add the WebSocket extension (right-click the main window, then Extensions) and select the hook that shows the game text.
2. Open TextLooker using one of the options in [Opening the page](#opening-the-page).
3. The status dot turns green when connected. New text from the game appears as new lines.
4. Hover over the text with Yomitan to look words up.

### Opening the page

There are three ways to open it.

**Option 1: Hosted copy (GitHub Pages).**
Open the live version linked at the top. Yomitan works on `https://` pages without any extra permission, and the page connects to your own machine, so use `ws://127.0.0.1:6677` in the URL field. Chrome and Firefox allow an HTTPS page to open a `ws://` connection to `127.0.0.1`, but Safari may block it, and newer versions of Chrome may ask you to allow access to local network devices the first time. If it doesn't connect, use Option 2 or 3.

**Option 2: Local web server.**
Download `index.html`, then serve its folder over HTTP:

```
cd /path/to/textlooker
python -m http.server 8000
```

Then open `http://127.0.0.1:8000/`.

**Option 3: Open the file directly (`file://`).**
In Chrome and other Chromium-based browsers, Yomitan won't run on local files by default. To allow it:

1. Open the browser's extension menu (`chrome://extensions`, or `edge://extensions` in Edge).
2. Find Yomitan and click **Details**.
3. Turn on **Allow access to file URLs**.
4. Reload the TextLooker page.

This permission is set per extension, so you only have to do it once.

## Configuration

Use the URL field at the top of the page. Enter a full address such as `ws://127.0.0.1:6677`. If you leave off the `ws://`, it is added for you. Press Enter or click **Reconnect** to apply.

## Troubleshooting

**Yomitan doesn't pop up on the text.**
Click **Test line**. If Yomitan doesn't work on the test line either, it isn't running on the page. Use the hosted copy, serve the page over HTTP, or enable the file URL permission described above. If the test line works but game text doesn't, the hook is probably producing garbled text (an encoding issue) or splitting characters apart, so try a different hook in Textractor.

**The status stays on "Retrying".**
Make sure the WebSocket extension is loaded in Textractor and that the host and port match. You can test the server independently with [websocat](https://github.com/vi/websocat):

```
websocat ws://127.0.0.1:6677
```

**Connected, but no text appears.**
The server only sends text from the hook selected in Textractor, and only when new text appears. Select the right hook and advance the game.

**Textractor under Wine crashes on connect.**
Try `127.0.0.1` instead of `localhost`, a different browser, or a different Wine version. If a hand-made handshake (for example with `curl`) also crashes it, the problem is in the server extension rather than this page.

## Credits

Inspired by [texthooker-ui](https://github.com/Renji-xD/texthooker-ui). TextLooker is a separate, minimal implementation and not a fork.

## License

MIT License. See [LICENSE](LICENSE).
