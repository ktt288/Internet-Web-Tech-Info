# java script

## java scriptとは
- クライアントサイドスクリプト
  - ブラウザのWeb Browser APIをキックし、Webページをダイナミックに動かすことができる
  - WebページはHTML、CSSだけでも成り立つが、java scriptが加わると動的な表現が可能となる
- サーバサイドスクリプト
  - サーバのnode.js APIをキックしOSの機能を活用しながら、様々な処理を実行することができる
- 図：カゴヤ・ジャパンサイトより
![](https://www.kagoya.jp/howto/wp-content/uploads/kagoya201910-5.png)
- 

## 基本的な使い方
- html上にjavascriptを記載する場合のサンプルコード

    ```
    <!doctype html>
    <html>
    <head>
    <meta charset="UTF-8">
    <title>Sample</title>
    </head>

    <body>
    <h1>ドキュメントにテキストを加える</h1>
    <script>
    document.write('このテキストが表示される');
    </script>
    </body>
    </html>
    ```

- scriptタグで囲む
- scriptタグの中身は
  - documento: オブジェクトと呼ぶ。操作する対象を示している。
  - write: メソッドと呼ぶ。操作/命令を示す。
  - (): オブジェクトに対し何をメソッド（実行）するかを指定する。指定なしの場合もある。文字列指定の場合はシングルクォーテーションで囲む（ダブルも可）
  - 今回の例では、
    - documentにテキストをwriteする、という指示をしている。
  - scriptの最後に;を入れる。（最近は入れなくても良いという考え方もある）
  - 
