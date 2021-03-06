下記のようなメソッドを記述していませんか。

```
C_BLOB($send)
TEXT TO BLOB("<html>"+$cr;$send;3) 
//...
TEXT TO BLOB("</html>"+$cr;$send;3) 

SEND HTML BLOB($send;"";False)
```
[ドキュメント](http://doc.4d.com/4Dv12/4D/12.4/Web-Context.301-977171.ja.html)に明記されているように，v12では__コンテキストモードが廃止__されました。変換されたDBでは，引き続きコンテキストモードを使用することができますが，新規データベースでこれを有効にすることはできません。

[SEND HTML BLOB](http://doc.4d.com/4Dv13/4D/13.5/WEB-SEND-BLOB.301-1457398.ja.html)の第3引数に``False``を渡すのは，「コンテキストモードを使用」という意味ですが，v12では，コンテキストモードそのものが廃止されているので，セッションを保持することにはなりません。コンテキストモードは，Webコンテキスト（プロセス変数やセレクションなどの情報を後続のアクセスまで保持すること）を実現するための__簡易的なメカニズム__でした。具体的には，URLの末尾にコンテキストIDを付加し，しばらくの間，そのページからのリクエストを処理するためのプロセスを待機させておき，続くリクエストを処理させることにより，セッション情報を保持するというものでした。コンテキストモードは，__Webテクノロジー最初期__に登場した，ごく簡易的なセッション管理の手法に基づいたものであり，「戻る」ボタンがクリックできないなど，さまざまな問題点を抱えているため，__v6.7以降__，ほとんど使用されていません。現在，大多数の4Dデベロッパーは，__非コンテキストモード__でWebアプリケーションを開発しています。

上記のように[TEXT TO BLOB](http://doc.4d.com/4Dv13/4D/13.5/TEXT-TO-BLOB.301-1457496.ja.html)でBLOBを構築するようなコードは，文字コード変換とメモリの再確保を何度も繰り返すことになるため，あまり効率的ではありません。HTMLを動的に出力したいのであれば，[4D TAGS](http://doc.4d.com/4Dv13/4D/13.5/PROCESS-4D-TAGS.301-1457565.ja.html)と__テンプレート__を使用したほうがずっと高速です。なお，```TEXT TO BLOB```は，互換性のためだけに残されている__古いコマンド__です。TEXTからBLOBに文字列を変換するのであれば，[CONVERT FROM TEXT](http://doc.4d.com/4Dv13/4D/13.5/CONVERT-FROM-TEXT.301-1457676.ja.html)を使用してください。

---

__解決策__

前述したように，コンテキストモードは，すでに廃されたテクノロジーです。新規DBの開発では，もっと手軽で強力な[自動セッション管理](http://doc.4d.com/4Dv13/4D/13.5/Web-Sessions-Management.300-1457382.ja.html)を使用するようにしてください。（v13以降）

__例題__

* [context-session](https://github.com/4D-JP/context-session)
