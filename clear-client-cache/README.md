``4DLink``ファイルの``clear_cache_folder``属性を``true``に指定してください。

### 解説

4D Remoteは，最新のストラクチャ情報（フォーム・メソッド・リストなど）を必要なときにサーバーから取り寄せています。クライアントキャッシュと呼ばれるこのデータは，クライアントデータベースフォルダに蓄積されてゆき，4D Serverとのネットワークアクセスを最適化するために活用されています。クライアントデータベースフォルダの場所は，``Get 4D folder``で確認することができます。

<!-- Generator: GNU source-highlight 3.1.6
by Lorenzo Bettini
http://www.lorenzobettini.it
http://www.gnu.org/software/src-highlite -->
<pre><tt><b><font color="#009900">Get 4D folder</font></b><font color="#000000">(</font><u><font color="#993399">4D Client Database Folder</font></u><font color="#000000">)</font></tt></pre>

本来，このフォルダを明示的に更新する必要はないはずです。4D Remoteは，リソースのバージョンを確認しており，適宜，最少限のファイルを更新しています。サーバーとの接続時，このフォルダが空であれば，4D Serverは基本的なストラクチャ情報・コンポーネントすべて・プラグインすべて・Resourcesフォルダ・Extrasフォルダの内容を圧縮（拡張子4DCache）し，クライアントに転送しなければなりません。クライアントは，すべてのファイルを展開し終えるまで，アプリケーションを開始することができません。

開発中は，ストラクチャが更新された後，またはコンポーネントやプラグインを入れ替えた後に，クライアントキャッシュを「強制的に」更新したいと思うかもしれません。そのようなときは，サーバー接続ダイアログ，カスタム画面のチェックボックスを有効にすることができます。

![connect](https://user-images.githubusercontent.com/10509075/38069800-4e8cbbf8-3353-11e8-9c9e-71dfc0b11736.png)

同じサーバーを頻繁に使用するのであれば，毎回，サーバー接続ダイアログを表示するよりも，4DLinkファイルを開いたほうが便利です。4DLinkファイルは，バージョン2004以前のパスファイル（拡張子PTH）に代わるもので，特定のアプリケーションを開始する「ショートカット」の役割を果たします。ファイルはXML形式であり，下記のスタートアップ情報を含めることができます。

```xml
    is_remote             : true = リモートデータベース          
    user_name             : ユーザー名
    password              : パスワード
    md5_password          : 暗号化されたパスワード
    structure_opening_mode: 0 = 前回と同じモード
                            1 = インタプリターモード
                            2 = コンパイルモード

    server_database_name  : サーバー公開データベース名
    server_path           : IPアドレスあるいはDNS名        
    open_login_dialog     : true = ログイン画面を開く  
  
    open_in_custom_mode    : アプリケーションモードで開始
    open_tools             : MSCを開く
    create_structure_file  : 新規ストラクチャファイルを作成
    structure_file         : ストラクチャファイルパス
    create_data_file       : 新規データファイルを作成
    data_file              : データファイルパス
    skip_onstartup_method  : On Startupを実行しない
    definition_import_file : ストラクチャ定義XMLファイルのパス（新規作成する場合）
    resources_import_file  : リソースファイルの場所（使用しないでください）
    data_opening_mode      : 1 = 前回と同じモード
                             2 = データファイルを選択する
                             3 = データファイルを作成する 
    data_conversion_mode   : 0 =データ変換ダイアログを表示する
                             1 = データ変換を実行しない
                             2 = ダイアログを表示せずにデータ変換を実行する
    structure_conversion_mode   : 0 = ストラクチャ変換ダイアログを表示する
                                  1 = ストラクチャ変換を実行しない
                                  2 = ダイアログを表示せずにストラクチャ変換を実行する 
```
    
この情報は，アプリケーション内部のResources/DTD/database_link.dtdファイルに記述されています。  

さらに，``clear_cache_folder``属性を``true``にすれば，クライアントキャッシュを強制的に更新させることができます。

XML形式の4DLinkファイルは，テキストエディターなどで自作することもできますが，一度でも接続したことのあるリモートデータベースであれば，``Alt (option)``キーを押しながら「ファイル/最近使用したデータベース」メニューを選択することにより，このメニューが内部的に使用している4DLinkをエクスプローラー (Finder) に表示させることができます。特別な設定値でサーバーに接続したいのであれば，このファイルをデスクトップなどにコピーし，必要な属性を編集すると良いでしょう。
