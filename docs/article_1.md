# Web Performance

## Web Performanceとは？
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
  - [Navigation Timing API](https://www.w3.org/TR/navigation-timing/)を使うことでこれを知ることができる（W3 / Navigation timingページより）
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

## Webサイトの表示をリクエストしてから表示が完了するまで
- ブラウザがWebページをリクエストしてから表示されるまで、大まかに言うと4つの工程がある。
  - Loading
  - Scripting
  - Rendering
  - Painting

### Loading
- この工程では主に2つの処理がある。
  - リソースのダウンロード
  - リソースのパース
#### リソースのダウンロード
  - リソースのダウンロードでは、Webページを構成する様々なファイルをWebサイトから取得する
  - 具体的には
    - HTMLファイル
    - CSSファイル
    - JavaScriptファイル
    - JPEG、PNG、GIF、SVGなどの画像ファイル
  - これらの取得は以下のステップで実行される。
    - DNSの名前解決でWebサイトのIPアドレスを調べる
    - WebサイトとTCP/TLSのコネクションを確立する
    - HTTPでファイルのリクエストを投げる
    - HTTPでファイルのレスポンスを受け取る

##### DNS
- Webページをリクエストする場合、ブラウザのアドレスバーにそのページのURLを入力する
- 具体的には以下のようなURL
  - https://example.jp/foobar.html
- URLを手動で入力することもあれば、検索エンジンのリンクから指定されることもある
- このURLは以下の要素で構成されている
  - https -> Webサイトとのファイルのやりとりで利用するプロトコルを示す
  - example.jp -> WebサイトのFQDNを示す
  - /foobar.html -> ファイルのディレクトリ/ファイル名を示す
- このWebサイトにWebページをリクエストする際、IPアドレスが必要
- IPアドレスはFQDNに紐づく
- FQDNに紐づくIPアドレスを調べるためにDNSを利用する
- 通常、PCは自身が利用するキャッシュDNSサーバのアドレス情報を予め持っている。
  - 契約しているISPがキャッシュDNSサービスを提供していることが多い
  - ISPより提供されるキャッシュDNSサーバのアドレスを自身のネットワーク設定に追加している
- このDNSサーバにFQDNに紐づくIPアドレスを問い合わせる
- IPアドレスを取得するまでの流れは、以下の通り（JPRSサイトより）
![](https://jprs.jp/glossary/imgs/stub-resolver.png)
  - PC（DNSクライアント）がキャッシュDNSサーバに問い合わせる
  - キャッシュDNSサーバは自身の持つroot DNSサーバに対し問い合わせる
  - root DNSサーバは当該TLD（トップレベルドメイン）を管理するDNSサーバのIPを応答する
  - キャッシュDNSサーバはTLDを管理するDNSサーバに対し問い合わせをする
  - TDLを管理するDNSサーバは当該ドメイン（この例ではexample.jp）を管理するDNSサーバのIPを応答する
  - キャッシュDNSサーバはexample.jpを管理するDNSサーバに対し問い合わせをする
  - example.jpを管理するDNSサーバはexample.jpに紐づくIPを応答する
- DNSによる名前解決において、パフォーマンスで注意すべきポイントは以下の通り
  - DNSキャッシュを活用する
    - 上述の通り、FQDNに紐づくIPの調査には時間を要する
    - 一度調査したIPは一定時間キャッシュすることでこの時間をスキップできる
    - キャッシュ場所は主に2箇所。PCとキャッシュDNSサーバ。
    - PCでキャッシュすることで、当該PCは毎回名前解決が不要となる。
    - キャッシュDNSサーバでキャッシュすることで、複数のクライアントが名前解決の結果を高速で受け取ることができる。
    - キャッシュ時間は、example.jpのドメイン管理者が決め、それに従いPC、キャッシュDNSサーバはキャッシュする。
- （グローバルに提供するWebサイトの場合）example.jpを管理するDNSサーバのロケーションに注意する
  - DNSは通常UDPを利用する
  - よってレイテンシーの影響は比較的受けにくい
  - しかしレイテンシーは小さい方がパフォーマンスは良い
  - Webサイト利用者がグローバルに存在する場合、単一ロケーションにDNSサーバがあることでレイテンシによりパフォーマンスが悪くなる場合がある
  - そこで、DNSのクラウドサービスを利用しエンドユーザに近いDNSサーバより応答可能とするなどの考慮をすることで、パフォーマンスが改善できるケースがある

##### TCP(TCP/TLS)

##### HTTP

#### リソースのパース


### scripting
### Rendering
### Painting

## onLoad


## memo
1. Event	
- 下記はどういうタイミングで発火するか？
  - DOMContentLoaded
  - load
  - jQuery の $(document).ready はどのイベントと等価か？
- イベントハンドラとは何か？
- イベントハンドラの実行時間が長いと、Core Web Vitals のどの指標が悪化するか？



2.	ページのロード過程
- HTML をロードしている最中に下記のエレメントが出現したとき、どのような挙動になるか
  - script
  - link rel=""stylesheet""
- script エレメントに async, defer 属性をつけると、どのような挙動になるか
- async, defer 属性をつけたときの注意点は？
- Script Management の Defer は、script エレメントに対してどのような影響を与えるか

[参考](https://developers.google.com/web/fundamentals/performance/critical-rendering-path)