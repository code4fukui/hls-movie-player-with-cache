# hls-movie-player-with-cache

Service Workerを使用して動画セグメントをキャッシュする、軽量なクライアントサイドHLS動画プレイヤーです。これにより、2回目以降の読み込みが高速化され、帯域幅の使用量を削減できます。

## デモ

- [Blowens 2024 Championship Canon R5 + Dual Fisheye 8K](https://code4fukui.github.io/hls-movie-player-with-cache/#demo/2G7A0207.m3u8)
- [Blowens 2024 Champion Canon R5 + Dual Fisheye 4K](https://code4fukui.github.io/hls-movie-player-with-cache/#demo/2G7A0214.m3u8)

## 機能

- **HLS再生:** モダンブラウザでHLS（m3u8）動画ストリームを再生します。
- **Service Workerによるキャッシュ:** HLS動画セグメント（`.ts`ファイル）を積極的にキャッシュし、再視聴時の読み込みを大幅に高速化し、オフライン再生の可能性を提供します。
- **スマートなフォールバック:** ブラウザのネイティブHLSサポート（例: Safari）を自動的に利用し、必要に応じて同梱の `hls.js` ライブラリにフォールバックします。
- **依存関係ゼロ:** ビルドツールやパッケージマネージャーを必要としない、ピュアJavaScriptのソリューションです。

## 必要条件

ES ModulesとService Workerをサポートするモダンウェブブラウザ。

## 仕組み

このプロジェクトは以下の2つの主要な部分から構成されています:
1.  `setVideoSrc.js`: ネイティブのHLSサポートを検出し、フォールバックとして `hls.js` を初期化するプレイヤーロジックです。
2.  `cache-service-worker.js`: 動画セグメントの `fetch` リクエストをインターセプトするService Workerです。セグメントがリクエストされると、まずキャッシュを確認します。見つかった場合は即座に提供され、見つからない場合はネットワークから取得してプレイヤーに提供し、今後のリクエストのためにキャッシュに保存します。

## 使い方

1.  `setVideoSrc.js`、`hls-es.js`、`cache-service-worker.js` をウェブサーバーに配置します。
2.  `<video>` 要素を含むHTMLファイルを作成します。
3.  Service Workerを登録し、`setVideoSrc` を呼び出して再生を開始するスクリプトを追加します。

**例 `index.html`:**

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

## 帰属

このプロジェクトには、バンドル版の `hls.js` が含まれています。詳細については、[video-dev/hls.js](https://github.com/video-dev/hls.js) を参照してください。

## ライセンス

MIT License
