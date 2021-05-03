# Web Performance

## What is Web Performance?
- Webサイトが遅い！原因はどこ？
  - ネットワークの帯域が細いからじゃない？帯域増やした方が良いのでは？
  - いやいや、サーバスペックが低すぎでしょ。ハイスペックにした方が良いのでは？
  - いやー、アプリケーションの作りが悪いんじゃ？アプリケーション改善が必要では？
  - レイテンシーも関係するのでは...CDNを利用しないとだめでは？
  - ブラウザでのスクリプト実行時間も関係するよね。
  - 3rdパーティースクリプトも関係するの...？
- 上記全て原因かもしれない
- Webサイトのパフォーマンスは様々な要素が積み重なって決まる
- Webサイトの表示をリクエストしてから表示が完了するまで、どんなことがどんな時間をかけて実行されているのか
  - [Navigation Timing API](https://www.w3.org/TR/navigation-timing/)を使うことでこれを知ることができる
![](https://www.w3.org/TR/navigation-timing/timing-overview.png)


  - domainLookupStart: DNS名前解決を開始する時点を示す
  - domainLookupEnd: DNS名前解決の完了時点を示す
  - connectStart: TCP 3way handshakeを開始する時点を示す
  - secureConnectionStart: TCPコネクションが確立し、TLS handshakeを開始する時点を示す
  - connectEnd: TCP/TLSコネクションが確立した時点を示す
  - requestStart: HTTPリクエストを投げ始める時点を示す
  - responseStart: HTTPレスポンスの1バイト目を受け取った時点を示す
  - responseENd: HTTPレスポンスを受領仕切った時点を示す
  - domLading: ブラウザがHTMLドキュメントのバイトの解析を開始する時点を示す
  - domInteractive: ブラウザがHTMLの解析を全て完了し、DOMの構築が完了した時点を示す
  - domCONtentLoaded: DOMの準備が整い、JavaScriptの実行をブロックするスタイルシートが存在しなくなった時点を示す
    - これ以降、レンダリングツリーの構築が開始できる
    - 多くのJavaScriptフレームワークでは、このイベントを待った上で、それ自体のロジックの実行を開始する
      - このため、EventStartとEventEndのタイムスタンプをキャプチャすることで、この処理の所要時間をトラッキングできる
  - domComplete: 全ての処理が完了し、ページ上にある全てのリソース（画像など）のダウンロードが完了したことを示す。読み込み中のマークの回転が止まった状態
  - loadEvent: ページごとの読み込みの最終ステップとして、ブラウザはonloadイベントを発行する。これにより追加のアプリケーションロジックがトリガーされることがある。
  - [参考サイト](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/measure-crp?hl=ja#navigation-timing)

## DNS

## TCP(TCP/TLS)

## HTTP

## Processing
### Loading
### scripting
### Rendering
### Painting

## onLoad


## memo
1.	Event	
  1. 下記はどういうタイミングで発火するか？
  　・DOMContentLoaded
  　・load
  　・jQuery の $(document).ready はどのイベントと等価か？
  2. イベントハンドラとは何か？
  3. イベントハンドラの実行時間が長いと、Core Web Vitals のどの指標が悪化するか？



2.	ページのロード過程
  1. HTML をロードしている最中に下記のエレメントが出現したとき、どのような挙動になるか
  　・script
  　・link rel=""stylesheet""
  2. script エレメントに async, defer 属性をつけると、どのような挙動になるか
  3. async, defer 属性をつけたときの注意点は？
  4. Script Management の Defer は、script エレメントに対してどのような影響を与えるか
