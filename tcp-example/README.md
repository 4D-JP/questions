
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

4D Internet CommandsのTCPコマンドは，SSL通信をサポート（4D本体のOpenSSLライブラリを使用）していますが，クライアント証明書には対応していません。証明書を使用するのであれば，無料の簡易ソフトである4D Internet Commandsではなく，ずっと高機能で本格的な[NTK](https://www.pluggers.nl/product/ntk-plugin/)プラグインの使用を検討してください。

TCP/IPクライアント
---


**例題**

[tcp-example](https://github.com/4D-JP/tcp-example)
