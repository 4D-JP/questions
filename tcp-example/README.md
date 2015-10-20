
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

ASSERT(TCP_Connect ($params);Attr ($params;"error.message"))

ASSERT(TCP_Get ($params);Attr ($params;"error.message"))

ASSERT(TCP_Disconnect ($params);Attr ($params;"error.message"))
```


**例題**

[tcp-example](https://github.com/4D-JP/tcp-example)
