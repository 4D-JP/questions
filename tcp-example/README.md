
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
C_OBJECT($params)

OB SET($params;"address";"127.0.0.1")
OB SET($params;"port";7777)
OB SET($params;"isSSL";False)

ASSERT(TCP_Connect ($params);Attr ($params;"error.message"))
```

```
//TCP_Connect

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

**参考**: [オブジェクト型を理解する](http://www.4d.com/jp/blog/c-object.html)

ネットワークが利用できない，サーバーが応答しないなど，接続が失敗した場合には，```False```が返されます。その場合，引数の```error.code```プロパティにはエラーコード，```error.message```にはエラーメッセージが代入されています（```TCP_Result```メソッド）。開発中は，[ASSERT](http://doc.4d.com/4Dv15/4D/15/ASSERT.301-2007074.ja.html)でこれを検知することができます。

```
ASSERT(TCP_Connect ($params);Attr ($params;"error.message"))
```

```Attr```は，ドット記法を処理するための簡易的なメソッドです。v15の時点で，オブジェクト型（JSON）のドット記法に対応しているのは，[QUERY BY ATTRIBUTE ](http://doc.4d.com/4Dv15/4D/15/QUERY-BY-ATTRIBUTE.301-2005959.ja.html)だけなので，通常は[OB Get](http://doc.4d.com/4Dv15/4D/15/OB-Get.301-2007253.ja.html)を多重にコールする必要があります。

```
//Attr

C_OBJECT($1)
C_TEXT($2)
C_TEXT($0)

$i:=0x0001
ARRAY LONGINT($pos;0)
ARRAY LONGINT($len;0)

$object:=$1

While (Match regex("([^.]+)";$2;$i;$pos;$len))
  $propertyName:=Substring($2;$pos{1};$len{1})
  If (OB Get type($object;$propertyName)=Is object)
    $object:=OB Get($1;$propertyName)
  Else 
    $0:=OB Get($object;$propertyName;Is text)
  End if 
  $i:=$pos{1}+$len{1}
End while 
```

接続に成功したならば，すぐにデータを送信することができます。クライアントとサーバーの間で交わされるメッセージには，通常，データの終わりを示すための方法がプロトコルで定められているはずです。この例では，単純に```0x00```がデータの終わりであるものとしています。

```
OB SET($params;"timeout";10)  //seconds
OB SET($request;"message";"123,abc,456,def"+Char(0))  //custom protocol
OB SET($request;"ie";"utf-8")
OB SET($request;"oe";"utf-8")
OB SET($params;"request";$request)

ASSERT(TCP_Get ($params);Attr ($params;"error.message"))
```

```
//TCP_Get

C_OBJECT($1)
C_BOOLEAN($0)

C_LONGINT($timeout)
C_TEXT($encoding)

$tcpId:=OB Get($1;"id";Is longint)
$timeout:=OB Get($1;"timeout";Is longint)*1000
$request:=OB Get($1;"request";Is object)
$message:=OB Get($request;"message")
$encoding:=OB Get($request;"ie";Is text)

C_BLOB($requestData;$responseData;$responseDataBuffer)

ON ERR CALL("ICU_ERR")
CONVERT FROM TEXT($message;$encoding;$requestData)
ON ERR CALL("")

C_LONGINT($tcpState)

If (OK=1)
  $error:=TCP_SendBLOB ($tcpId;$requestData)

  If (TCP_Result ($1;$error))

    $start:=Milliseconds

    Repeat 

      DELAY PROCESS(Current process;12)

      $error:=TCP_ReceiveBLOB ($tcpId;$responseDataBuffer)
      $error:=TCP_State ($tcpId;$tcpState)

      COPY BLOB($responseDataBuffer;$responseData;0;BLOB size($responseData);BLOB size($responseDataBuffer))
      CLEAR VARIABLE($responseDataBuffer)

      $timeoutReached:=(Milliseconds-$start)>$timeout

    Until (($error#0) | ($tcpState=0) | ($timeoutReached))

    If ($timeoutReached)

      $0:=TCP_Failure ($1;-1;"timeout reached!")

    Else 

      $encoding:=OB Get($request;"oe")

      ON ERR CALL("ICU_ERR")
      OB SET($1;"response";Convert to text($responseData;$encoding))
      ON ERR CALL("")

      If (OK=1)
        $0:=TCP_Result ($1;$error)
      Else 
        $0:=TCP_Failure ($1;ERROR;ERROR TEXT)
      End if 

    End if 

  End if 

Else 
  $0:=TCP_Failure ($1;ERROR;ERROR TEXT)
End if 
```

送信データは，```TCP_SendBLOB```でまとめて処理することができます。この例では，もとのデータがテキスト（CSV）なので```CONVERT FROM TEXT```でこれをBLOBに変換しています。文字コードの変換は，エンコーディングがサポートしていない文字が渡された場合など，失敗する可能性があるので，```ON ERROR CALL```も使用しています。

**注記**

``TCP_Send``は，Unicodeモード以前からの遺産なので，通常は使用しません。

サーバーからの返信は，時間をかけて受信する必要があります。具体的にはバッファBLOBを用意し，エラーが返されるか，TCP接続が失われるまで，```TCP_ReceiveBLOB```を繰り返しコールします。加えてタイムアウトも設定すると良いでしょう。BLOBの連結には```COPY BLOB```を使用します。コールの間隔を空けるために```DELAY PROCESS```をループ内に記述してください。

最後に```TCP_Close```で接続を閉じて完了です。

``TCP_Disconnect``は，オブジェクト型のパラメーターを受け渡しするラッパーメソッドです。 

```
C_OBJECT($1)
C_BOOLEAN($0)

$tcpId:=OB Get($1;"id";Is longint)

$error:=TCP_Close ($tcpId)

$0:=TCP_Result ($1;$error)
```

TCP/IPサーバー
---

TCPクライアントのプログラムは，サーバーが応答しなければ動きません。動作を確認するために，4D Internet Commandsのローレベルコマンドで簡易的なTCPサーバーを作成するとデバッグが容易になります。

**注記**

4D Internet Commandsは，極めて簡易な受信コマンドしか提供していません。その非力なコマンド群でサーバーソフトウェアを構築することには，明らかに無理があります。同時に数件のリクエストを処理しただけでパケットロスが発生したり，過剰にリソースを消費することになりかねません。**サーバーソフトウェア**を構築するのであれば，無料の簡易ソフトである4D Internet Commandsではなく，ずっと高機能で本格的な[NTK](https://www.pluggers.nl/product/ntk-plugin/)プラグインの使用を検討してください。

```
//TCP_SERVER

C_OBJECT($1)
C_LONGINT($2)

$params:=Count parameters

Case of 
 : ($params=1)

  HIDE PROCESS(New process(Current method name;0;"$TCP_LISTEN";$1;Current process;*))

 : ($params=2)

  $port:=OB Get($1;"port";Is longint)
  $timeout:=OB Get($1;"timeout";Is longint)
  $hostName:=""
  $portNumberLocal:=0x0000

  C_LONGINT($tcpId;$tcpState)

  $error:=TCP_Listen ($hostName;$portNumberLocal;$port;$timeout;$tcpId)

  If ($error=0)

    $error:=TCP_State ($tcpId;$tcpState)

    If ($tcpState=8)

      C_BLOB($requestData;$requestDataBuffer;$responseData)
      C_TEXT($message)

      Repeat 

        $error:=TCP_ReceiveBLOB ($tcpId;$requestDataBuffer)
        $error:=TCP_State ($tcpId;$tcpState)

        COPY BLOB($requestDataBuffer;$requestData;0;BLOB size($requestData);BLOB size($requestDataBuffer))
        CLEAR VARIABLE($requestDataBuffer)

        $lastBytePosition:=BLOB size($requestData)-1
        $completeMessage:=$requestData{$lastBytePosition}=0x0000

        If ($completeMessage)
          DELETE FROM BLOB($requestData;$lastBytePosition;1)
          ON ERR CALL("ICU_ERR")
          $message:=Convert to text($requestData;"utf-8")
          ON ERR CALL("")
        End if 

      Until ($error#0) | ($tcpState=0) | ($completeMessage)

      C_OBJECT($response)
      OB SET($response;"receivedFullRequest";$completeMessage)
      OB SET($response;"message";$message)
      CONVERT FROM TEXT(JSON Stringify($response);"utf-8";$responseData)

      $error:=TCP_SendBLOB ($tcpId;$responseData)

    End if 

    $error:=TCP_Close ($tcpId)

  End if 

End case 
```

``TCP_Listen``は，制御が返されるまでプロセスをブロックするので，新規プロセスで使用します。その後，クライアント側のときと同じように``TCP_ReceiveBLOB``を繰り返しコールします。この例題では，単純に受信したメッセージ（末端の```0x00```を除く）をそのまま返しています。ここでもオブジェクト型（```JSON Stringify```）が使用されていることに注目してください。JSON形式のオブジェジェクト型は軽量で可読性が高く，構造を容易に拡張することができるので非常に便利です。

**例題**

[tcp-example](https://github.com/4D-JP/tcp-example)
