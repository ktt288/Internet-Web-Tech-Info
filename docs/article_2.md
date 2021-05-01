# Boomerang

## Boomerangとは
- boomelangはJavaScript
- boomelangはオープンソース
- boomelangはウェブページに埋め込んで使う
- boomelangはウェブページのパフォーマンスを測定できる

## パフォーマンス測定の方法
- onbeforeunloadイベントからonloadイベントまでを測定する
  - onbeforeunloadイベント（現在のウェブページから、他のウェブページに遷移する直前に発生するイベント）
  - onloadイベント（htmlや画像の読み込みが完了した、すなわちあるウェブページの表示が完了した際に発生するイベント）
  - この間における様々なタイミングデータを取得することができる。例えば、
    - DNSの名前解決に要した時間
    - TCP接続に要した時間
    - SSL接続に要した時間
    - 最初の1バイトを受領するまでの時間
    - FCP/LCP...などなど
  
## シングルページ
- デスクトップアプリケーションのようなユーザ体験が得られる
- 一番最初のページ表示は通常のウェブサイトと概ね変わらない
- その後の画面遷移は、スクリプトを実行し取得済みのコンテンツに差し替えるといった動作となる
- Onbeforeunloadイベントやonloadイベントがなく、計測ができない...？
- boomelangはSPA hardナビゲーション、SPA softナビゲーションによりシングルページの画面遷移時の情報も取得することができる
  - 初回ページ表示はSPA hard
  - その後のページ遷移はSPA soft
- ただし、DOMcontentloardedイベントを伴わないページ遷移においてTTIなど一部データは存在しないことになり、当然取得もされない（古いboomerangバージョンで取得されることもあったが、正しくはない）
- [TTI](https://github.com/WICG/time-to-interactive/blob/master/README.md)
- TTIのイメージ（上記サイトより）
![](/2021-04-13-21-55-31.png)

## 参考にしたサイト
- http://pogs.mit.edu/static/js/boomerang/doc/ja/
- https://developer.akamai.com/tools/boomerang/release-notes/#1.700.0-(january-29,-2020)
- https://akamai.github.io/boomerang/BOOMR.plugins.SPA.html#toc2__anchor
