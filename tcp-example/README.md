
[4D Internet Commands](http://doc.4d.com/4Dv15/4D-Internet-Commands/15/4D-Internet-Commands.100-2397788.ja.html)で実装することができます。

---

```
C_OBJECT($params;$request)

  //connection parameters
OB SET($params;"address";"127.0.0.1")
OB SET($params;"port";7777)
OB SET($params;"isSSL";False)
OB SET($params;"timeout";10)  //seconds

  //message parameters
OB SET($request;"message";"123,abc,456,def"+Char(0))  //custom protocol
OB SET($request;"ie";"utf-8")
OB SET($request;"oe";"utf-8")
OB SET($params;"request";$request)

ASSERT(TCP_Connect ($params);Attr ($params;"error.message"))  //connect

ASSERT(TCP_Get ($params);Attr ($params;"error.message"))  //send request and receive response

ASSERT(TCP_Disconnect ($params);Attr ($params;"error.message"))  //disconnect
```

**概要**

4D Internet Commandsの[ローレベルTCPコマンド](http://doc.4d.com/4Dv15/4D-Internet-Commands/15/Low-Level-Routines-Overview.300-2397929.ja.html)を使用すれば，POP3・IMAP・FTP・SMTPコマンド群でサポートされていない任意のソケット通信プログラムを記述することができます。実際，v13で[HTTP Get](http://doc.4d.com/4Dv15/4D/15/HTTP-Get.301-2007235.ja.html)が追加される以前は，HTTPクライアントも[TCPコマンドで作成する](http://kb.4d.com/assetid=39384)ことが一般的でした。この例題は，CSVデータをネットワーク上の機器に送信するようなプログラムを想定しています。

**注記**

4D Internet CommandsのTCPコマンドは，SSL通信をサポート（4D本体のOpenSSLライブラリを使用）していますが，クライアント証明書には対応していません。**証明書**を使用するのであれば，無料の簡易ソフトである4D Internet Commandsではなく，ずっと高機能で本格的な[NTK](https://www.pluggers.nl/product/ntk-plugin/)プラグインの使用を検討してください。

TCP/IPクライアント
---

クライアント側は，はじめにサーバーとのソケット接続を確立する必要があります。これには，```TCP_Open```を使用します。コマンドには，下記のパラメーターを渡します。

* ホスト名またはアドレス（テキスト）
* ポート番号（数値）
* SSL使用の是非（ブール）

``TCP_Connect``は，オブジェクト型のパラメーターを受け渡しするラッパーメソッドです。 

```
C_OBJECT($1)
C_BOOLEAN($0)

C_BOOLEAN($isSSL)

$address:=OB Get($1;"address";Is text)
$port:=OB Get($1;"port";Is longint)
$isSSL:=OB Get($1;"isSSL";Is Boolean)

C_LONGINT($tcpId)

If ($isSSL)
  $error:=TCP_Open ($address;$port;$tcpId;2)
Else 
  $error:=TCP_Open ($address;$port;$tcpId;0)
End if 

OB SET($1;"id";$tcpId)

$0:=TCP_Result ($1;$error)
```

**注記**

v14で新設されたオブジェクト型を使用すれば，数やタイプが特定されない任意の引数をメソッドに渡すことができます。オブジェクト型の引数は，実際には参照型なので，ポインター型を使用しなくても，直接，プロパティに値を代入することができます。

**参考**: [```オブジェクト型を理解する```](http://www.4d.com/jp/blog/c-object.html)

ネットワークが利用できない，サーバーが応答しないなど，接続が失敗した場合には，```False```が返されます。その場合，引数の```error.code```プロパティにはエラーコード，```error.message```にはエラーメッセージが代入されています（```TCP_Result```メソッド）。開発中は，[ASSERT](http://doc.4d.com/4Dv15/4D/15/ASSERT.301-2007074.ja.html)でこれを検知することができます。

```
ASSERT(TCP_Connect ($params);Attr ($params;"error.message"))
```

```Attr```は，ドット記法を処理するための簡易的なメソッドです。v15の時点で，オブジェクト型（JSON）のドット記法に対応しているのは，[QUERY BY ATTRIBUTE ](http://doc.4d.com/4Dv15/4D/15/QUERY-BY-ATTRIBUTE.301-2005959.ja.html)だけなので，通常は[OB Get](http://doc.4d.com/4Dv15/4D/15/OB-Get.301-2007253.ja.html)を多重にコールする必要があります。





TCP/IPサーバー
---





**注記**

4D Internet Commandsは，極めて簡易な受信コマンドしか提供していません。その非力なコマンド群でサーバーソフトウェアを構築することには，明らかに無理があります。同時に数件のリクエストを処理しただけでパケットロスが発生したり，過剰にリソースを消費することになりかねません。**サーバーソフトウェア**を構築するのであれば，無料の簡易ソフトである4D Internet Commandsではなく，ずっと高機能で本格的な[NTK](https://www.pluggers.nl/product/ntk-plugin/)プラグインの使用を検討してください。


**例題**

[tcp-example](https://github.com/4D-JP/tcp-example)