[Match regex](http://doc.4d.com/4Dv15/4D/15/Match-regex.301-2006648.ja.html)と正規表現で処理することができます。

4Dの変数やフィールドには，Unicodeで文字列が収められています。採用されているエンコーディングはUTF-16であり，ランゲージでは，バイト数ではなく，コードポイント数で文字列のサイズを計算します。日本語の全角/半角を含め，基本多言語面の文字はいずれもサイズが1ですが，拡張文字など，複数のコードポイントで表現されるものはサイズが2以上になることもあります。

4Dの内部文字コードがUnicodeであるとしても，外部アプリケーションと連携するため，あるいは既存のビジネスロジックを継承するために，半角1バイト，全角2バイトに換算して文字列のサイズを計算しなければならないことがあります。変換されたデータベースの場合，文字フィールドのサイズは，バイト数に基づいて定義されたものかもしれません。そのような場合，文字列のサイズをバイト数で計算することが必要です。

はじめに，代表的な文字列コマンド（[Length](http://doc.4d.com/4Dv15/4D/15/Length.301-2006662.ja.html)，[Substring](http://doc.4d.com/4Dv15/4D/15/Substring.301-2006645.ja.html)，[Position](http://doc.4d.com/4Dv15/4D/15/Position.301-2006659.ja.html)）の「バイト版」を作成しておくと便利です。

<!-- Generator: GNU source-highlight 3.1.6
by Lorenzo Bettini
http://www.lorenzobettini.it
http://www.gnu.org/software/src-highlite -->
<pre><tt><font color="#0000FF">$width</font><font color="#000000">:=</font><b><i><font color="#000080">TEXT_Get_width </font></i></b><font color="#000000">(</font><font color="#0000FF">$text</font><font color="#000000">)</font>
<font color="#0000FF">$substring</font><font color="#000000">:=</font><b><i><font color="#000080">TEXT_Get_substring_by_width </font></i></b><font color="#000000">(</font><font color="#0000FF">$text</font><font color="#000000">;</font><font color="#000000">4</font><font color="#000000">;</font><font color="#000000">2</font><font color="#000000">)</font>
<font color="#0000FF">$foundLen</font><font color="#000000">:=</font><font color="#000000">0</font>
<font color="#0000FF">$position</font><font color="#000000">:=</font><b><i><font color="#000080">TEXT_Get_position_by_width </font></i></b><font color="#000000">(</font><font color="#000000">"ｶ"</font><font color="#000000">;</font><font color="#0000FF">$text</font><font color="#000000">;</font><font color="#000000">1</font><font color="#000000">;-&gt;</font><font color="#0000FF">$foundLen</font><font color="#000000">)</font></tt></pre>

TEXT_Get_widthは，Lengthに対応するメソッドであり，全角は2，半角は1という換算で文字列のサイズを返します。Shift_JISエンコーディングのバイト数を知りたいのであれば，[CONVERT FROM TEXT](http://doc.4d.com/4Dv15/4D/15/CONVERT-FROM-TEXT.301-2006651.ja.html)で文字列を変換し，BLOBのサイズを調べることもできますが，その場合，Shift_JISに変換できない文字（?に変換）や，サロゲートペア文字（??に変換）に対応することができません。このメソッドでは，UnicodeをShift_JISに変換する代わりに，正規表現で解析することにより，そのような文字にも対応しました。

Unicodeでは，すべての文字にひとつ以上のプロパティが定義されています。全角/半角の属性に対応しているのは，East Asian Widthというプロパティです。このメソッドでは，便宜上，「半角ではない文字は全角である」とみなし，文字列に含まれる半角の数をカウントしています。

<!-- Generator: GNU source-highlight 3.1.6
by Lorenzo Bettini
http://www.lorenzobettini.it
http://www.gnu.org/software/src-highlite -->
<pre><tt><font color="#0000FF">$i</font><font color="#000000">:=</font><font color="#000000">1</font>

<b><font color="#009900">While</font></b> <font color="#000000">(</font><b><font color="#009900">Match regex</font></b><font color="#000000">(</font><font color="#000000">"(?:[[:East Asian Width=Halfwidth:][:East Asian Width=Narrow:]]+)"</font><font color="#000000">;</font><font color="#0000FF">$text</font><font color="#000000">;</font><font color="#0000FF">$i</font><font color="#000000">;</font><font color="#0000FF">$pos</font><font color="#000000">;</font><font color="#0000FF">$len</font><font color="#000000">))</font>
<font color="#0000FF">$countHalfWidth</font><font color="#000000">:=</font><font color="#0000FF">$countHalfWidth</font><font color="#000000">+</font><font color="#0000FF">$len</font><font color="#000000">{</font><font color="#000000">0</font><font color="#000000">}</font>
<font color="#0000FF">$i</font><font color="#000000">:=</font><font color="#0000FF">$pos</font><font color="#000000">{</font><font color="#000000">0</font><font color="#000000">}+</font><font color="#0000FF">$len</font><font color="#000000">{</font><font color="#000000">0</font><font color="#000000">}</font>
<b><font color="#009900">End while</font></b> </tt></pre>

残りの文字は全角ですが，それにはサロゲートペアや，結合文字が含まれるかもしれません。そこで，正規表現の\X（グラフィームクラスター）を使用し，コードポイントの数（Length）ではなく，グリフの数に基づいて全角の数をカウントしています。

<!-- Generator: GNU source-highlight 3.1.6
by Lorenzo Bettini
http://www.lorenzobettini.it
http://www.gnu.org/software/src-highlite -->
<pre><tt><font color="#0000FF">$i</font><font color="#000000">:=</font><font color="#000000">1</font>

<b><font color="#009900">While</font></b> <font color="#000000">(</font><b><font color="#009900">Match regex</font></b><font color="#000000">(</font><font color="#000000">"([^[:East Asian Width=Halfwidth:][:East Asian Width=Narrow:]]+)"</font><font color="#000000">;</font><font color="#0000FF">$text</font><font color="#000000">;</font><font color="#0000FF">$i</font><font color="#000000">;</font><font color="#0000FF">$pos</font><font color="#000000">;</font><font color="#0000FF">$len</font><font color="#000000">))</font>
<font color="#0000FF">$fullWidthText</font><font color="#000000">:=</font><b><font color="#009900">Substring</font></b><font color="#000000">(</font><font color="#0000FF">$text</font><font color="#000000">;</font><font color="#0000FF">$pos</font><font color="#000000">{</font><font color="#000000">1</font><font color="#000000">};</font><font color="#0000FF">$len</font><font color="#000000">{</font><font color="#000000">1</font><font color="#000000">})</font>
<font color="#0000FF">$j</font><font color="#000000">:=</font><font color="#000000">1</font>
<font color="#0000FF">$i</font><font color="#000000">:=</font><font color="#0000FF">$pos</font><font color="#000000">{</font><font color="#000000">1</font><font color="#000000">}+</font><font color="#0000FF">$len</font><font color="#000000">{</font><font color="#000000">1</font><font color="#000000">}</font>
<b><font color="#009900">While</font></b> <font color="#000000">(</font><b><font color="#009900">Match regex</font></b><font color="#000000">(</font><font color="#000000">"</font><font color="#FF0000">\\</font><font color="#000000">X"</font><font color="#000000">;</font><font color="#0000FF">$fullWidthText</font><font color="#000000">;</font><font color="#0000FF">$j</font><font color="#000000">;</font><font color="#0000FF">$pos</font><font color="#000000">;</font><font color="#0000FF">$len</font><font color="#000000">))</font>
<font color="#0000FF">$countFullWidth</font><font color="#000000">:=</font><font color="#0000FF">$countFullWidth</font><font color="#000000">+</font><font color="#000000">1</font>
<font color="#0000FF">$j</font><font color="#000000">:=</font><font color="#0000FF">$pos</font><font color="#000000">{</font><font color="#000000">0</font><font color="#000000">}+</font><font color="#0000FF">$len</font><font color="#000000">{</font><font color="#000000">0</font><font color="#000000">}</font>
<b><font color="#009900">End while</font></b> 
<b><font color="#009900">End while</font></b> </tt></pre>

最後に半角1バイト，全角2バイトに換算して文字列のサイズを計算しています。

<!-- Generator: GNU source-highlight 3.1.6
by Lorenzo Bettini
http://www.lorenzobettini.it
http://www.gnu.org/software/src-highlite -->
<pre><tt><b><i><font color="#000080">$0</font></i></b><font color="#000000">:=</font><font color="#0000FF">$countHalfWidth</font><font color="#000000">+(</font><font color="#0000FF">$countFullWidth</font><font color="#000000">*</font><font color="#000000">2</font><font color="#000000">)</font></tt></pre>

SubstringやPositionも同じように正規表現で「バイト版」にすることができます。具体的な方法は，サンプルコードで確認してください。

以上は，「半角1バイト，全角2バイト」の概念をUnicodeに持ち込んだものであり，Shift_JISとは関係のないものでした。文字列を変換しているわけではないので，Shift_JISに変換できないUnicodeの文字も，半角/全角に区別することができます。もっとも，Shift_JISの外部アプリケーションに渡されるデータは，入力中，キャラクターセットの範囲を確認したいと思うかもしれません。サンプルには，そのためのメソッドも含まれています。

<!-- Generator: GNU source-highlight 3.1.6
by Lorenzo Bettini
http://www.lorenzobettini.it
http://www.gnu.org/software/src-highlite -->
<pre><tt><b><font color="#009900">ARRAY TEXT</font></b><font color="#000000">(</font><font color="#0000FF">$illegalChar</font><font color="#000000">;</font><font color="#000000">0</font><font color="#000000">)</font>
<b><font color="#009900">ARRAY LONGINT</font></b><font color="#000000">(</font><font color="#0000FF">$illegalPos</font><font color="#000000">;</font><font color="#000000">0</font><font color="#000000">)</font>
<b><font color="#009900">ARRAY LONGINT</font></b><font color="#000000">(</font><font color="#0000FF">$illegalLen</font><font color="#000000">;</font><font color="#000000">0</font><font color="#000000">)</font>

<b><i><font color="#000080">TEXT_FILTER_BY_ENCODING </font></i></b><font color="#000000">(</font><font color="#0000FF">$text</font><font color="#000000">;</font><font color="#000000">"windows-31j"</font><font color="#000000">;-&gt;</font><font color="#0000FF">$illegalChar</font><font color="#000000">;-&gt;</font><font color="#0000FF">$illegalPos</font><font color="#000000">;-&gt;</font><font color="#0000FF">$illegalLen</font><font color="#000000">)</font></tt></pre>

このメソッドは，指定したエンコーディングに変換できない文字列，および位置とサイズの配列を返します。

変換されたデータベースの場合，文字フィールドのサイズは，バイト数に基づいて定義されたものである場合が少なくありません。フィールドの文字サイズを返すコマンドは，[GET FIELD PROPERTIES](http://doc.4d.com/4Dv15/4D/15/GET-FIELD-PROPERTIES.301-2006631.ja.html)コマンドです。この値と前述したSubstringのバイト版を併用すれば，フィールドの文字列をUnicodeの文字数ではなく，「半角1バイト，全角2バイト」換算で管理することができます。

<!-- Generator: GNU source-highlight 3.1.6
by Lorenzo Bettini
http://www.lorenzobettini.it
http://www.gnu.org/software/src-highlite -->
<pre><tt><b><i><font color="#000080">OBJECT_FORCE_LIMIT_LENGTH </font></i></b><font color="#000000">(-&gt;</font><font color="#808080">[Table_1]Field_1</font><font color="#000000">)</font></tt></pre>

このメソッドは，On After Editイベントで実行されることが想定されています。

<!-- Generator: GNU source-highlight 3.1.6
by Lorenzo Bettini
http://www.lorenzobettini.it
http://www.gnu.org/software/src-highlite -->
<pre><tt><b><font color="#009900">C_POINTER</font></b><font color="#000000">(</font><b><i><font color="#000080">$1</font></i></b><font color="#000000">)</font>

<font color="#0000FF">$countParameters</font><font color="#000000">:=</font><b><font color="#009900">Count parameters</font></b>

<b><font color="#009900">If</font></b> <font color="#000000">(</font><font color="#0000FF">$countParameters</font><font color="#000000">#</font><font color="#000000">0</font><font color="#000000">)</font>
<b><font color="#009900">If</font></b> <font color="#000000">(</font><b><font color="#009900">Not</font></b><font color="#000000">(</font><b><font color="#009900">Nil</font></b><font color="#000000">(</font><b><i><font color="#000080">$1</font></i></b><font color="#000000">)))</font>
<b><font color="#009900">If</font></b> <font color="#000000">(</font><b><font color="#009900">Type</font></b><font color="#000000">(</font><b><i><font color="#000080">$1</font></i></b><font color="#000000">-&gt;)=</font><u><font color="#993399">Is Alpha Field</font></u><font color="#000000">)</font>
<b><i><font color="#000080">$1</font></i></b><font color="#000000">-&gt;:=</font><b><i><font color="#000080">TEXT_Get_substring_by_width </font></i></b><font color="#000000">(</font><b><font color="#009900">Get edited text</font></b><font color="#000000">;</font><font color="#000000">1</font><font color="#000000">;</font><b><i><font color="#000080">FIELD_Get_limit_length </font></i></b><font color="#000000">(</font><b><i><font color="#000080">$1</font></i></b><font color="#000000">))</font>
<b><font color="#009900">End if</font></b> 
<b><font color="#009900">End if</font></b> 
<b><font color="#009900">End if</font></b> </tt></pre>

サンプルは，コンポーネントになっており，各種ユーティリティが共有メソッドとして設定されていることに加え，入力を補助あるいは制御するためのウィジェットも用意されています。

テキスト変数の入力に「半角1バイト，全角2バイト」のチェックを追加するためには，その変数のOn After !Edit/On Getting !Focus/On Losing Focusイベントを有効にし，各イベントで記のコンポーネントメソッドが呼び出されるようにしてください。引数は，同じフォームに配置されたWarning Widthウィジェットのオブジェクト名と文字列の最大サイズ（バイト数）です。

<!-- Generator: GNU source-highlight 3.1.6
by Lorenzo Bettini
http://www.lorenzobettini.it
http://www.gnu.org/software/src-highlite -->
<pre><tt><b><i><font color="#000080">OBJECT_CHECK_WIDTH </font></i></b><font color="#000000">(</font><font color="#000000">"WidthWarning"</font><font color="#000000">;</font><font color="#000000">20</font><font color="#000000">)</font></tt></pre>

テキスト変数の入力にエンコーディングのチェックを追加するためには，その変数のOn After !Edit/On Getting !Focus/On Losing Focusイベントを有効にし，各イベントで記のコンポーネントメソッドが呼び出されるようにしてください。引数は，同じフォームに配置されたWarning Encodeウィジェットのオブジェクト名です。

<!-- Generator: GNU source-highlight 3.1.6
by Lorenzo Bettini
http://www.lorenzobettini.it
http://www.gnu.org/software/src-highlite -->
<pre><tt><b><i><font color="#000080">OBJECT_CHECK_ENCODING </font></i></b><font color="#000000">(</font><font color="#000000">"EncodeWarning"</font><font color="#000000">)</font></tt></pre>

文字フィールドのサイズをバイト数換算で制御するためには，そのフィールドのOn After Editイベントを有効にし，同イベントで下記のコンポーネントメソッドが呼び出されるようにしてください。

<!-- Generator: GNU source-highlight 3.1.6
by Lorenzo Bettini
http://www.lorenzobettini.it
http://www.gnu.org/software/src-highlite -->
<pre><tt><b><i><font color="#000080">OBJECT_FORCE_LIMIT_LENGTH </font></i></b><font color="#000000">(</font><b><font color="#009900">Self</font></b><font color="#000000">)</font></tt></pre>

入力を補助あるいは制御するためのコードは，ウィジェットのOn Bound Variable Changeイベントで自動的に実行されるので，ほぼノンプログラミングで「半角1バイト，全角2バイト」の概念をUnicodeに持ち込むことができます。

例題

* [byte-width](https://github.com/4D-JP/byte-width)


