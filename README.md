# hls-movie-player-with-cache

> 日本語のREADMEはこちらです: [README.ja.md](README.ja.md)

A lightweight, client-side HLS video player that uses a Service Worker to cache video segments, enabling faster subsequent loads and reduced bandwidth usage.

## Demo

- [Blowens 2024 Championship Canon R5 + Dual Fisheye 8K](https://code4fukui.github.io/hls-movie-player-with-cache/#demo/2G7A0207.m3u8)
- [Blowens 2024 Champion Canon R5 + Dual Fisheye 4K](https://code4fukui.github.io/hls-movie-player-with-cache/#demo/2G7A0214.m3u8)

## Features

- **HLS Playback:** Plays HLS (m3u8) video streams in any modern browser.
- **Service Worker Caching:** Aggressively caches HLS video segments (`.ts` files) for significantly faster re-watches and potential offline playback.
- **Smart Fallback:** Automatically uses the browser's native HLS support (e.g., Safari) and falls back to the bundled `hls.js` library where needed.
- **Zero Dependencies:** A pure JavaScript solution with no build tools or package manager required.

## Requirements

A modern web browser with support for ES Modules and Service Workers.

## How It Works

This project consists of two main parts:
1.  `setVideoSrc.js`: The player logic that detects native HLS support and initializes `hls.js` as a fallback.
2.  `cache-service-worker.js`: A service worker that intercepts `fetch` requests for video segments. If a segment is requested, it first checks the cache. If found, it's served immediately; otherwise, it's fetched from the network, served to the player, and stored in the cache for future requests.

## Usage

1.  Place `setVideoSrc.js`, `hls-es.js`, and `cache-service-worker.js` on your web server.
2.  Create an HTML file with a `<video>` element.
3.  Add a script to register the service worker and call `setVideoSrc` to start playback.

**Example `index.html`:**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>HLS Player with Cache</title>
</head>
<body>

  <h1>HLS Player</h1>
  <video id="video-player" controls width="800"></video>

  <script type="module">
    // 1. Register the service worker for caching
    if ('serviceWorker' in navigator) {
      navigator.serviceWorker.register('./cache-service-worker.js')
        .then(reg => console.log('Service worker registered:', reg))
        .catch(err => console.error('Service worker registration failed:', err));
    }

    // 2. Import the player function
    import { setVideoSrc } from './setVideoSrc.js';

    // 3. Get the video element and specify the HLS source
    const video = document.getElementById('video-player');
    const hlsSource = 'path/to/your/video.m3u8';

    // 4. Initialize the player
    setVideoSrc(video, hlsSource);
  </script>

</body>
</html>
```

## Attribution

This project includes a bundled version of `hls.js`. For more information, visit [video-dev/hls.js](https://github.com/video-dev/hls.js).

## License

MIT License.