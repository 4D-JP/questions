```PROCESS HTML TAGS```でXMLスプレッドシートを作成することができます。

Excelは，さまざまなファイルタイプをサポートしています。その中でも「Excel 2004 XML スプレッドシート (.xml)」形式は，4Dの標準XMLコマンドだけで読み書きすることができ，データとプレゼンテーション（テンプレート）を分離できるというメリットがあるため，多くのデベロッパに好まれているエクスポート形式です。

はじめに，Excel 2004 XML スプレッドシート (.xml)」形式の限界を理解しておくことは大切です。まず，この形式のスプレッドシートは，Excel 2004よりも古いバージョンでは開くことができません。次に，グラフや画像の挿入など，一部の機能がサポートされていません。また，ファイル拡張子が汎用的な「.xml」であるため，必ずしもユーザーによるファイルのダブルクリック操作でExcelが起動するわけではないという点にも留意する必要があります。そのような限界が問題にならないのであれば，Excel 2004 XML スプレッドシート (.xml)」形式のエクスポートは，とても手軽で実用的なテクニックです。

```PROCESS HTML TAGS```でXMLスプレッドシートを作成する場合，スプレッドシートに「差し込み」されるデータには，ピクチャ，BLOBタイプを除くタイプのフィールドあるいは配列を使用することができます。ここでは，簡単なテーブルをExcelに書き出したいと思います。

![](https://github.com/4D-JP/export-xml-for-excel/blob/master/images/table.png)

```PROCESS HTML TAGS```でXMLスプレッドシートを作成する場合，スプレッドシートに「差し込み」されるデータには，ピクチャ，BLOBタイプを除くタイプのフィールドあるいは配列を使用することができます。ここでは，簡単なテーブルをExcelに書き出したいと思います。

はじめに，書き出しファイルのイメージとなるスプレッドシートをMicrosoft Excelで作成します。前述したように，グラフや画像などを挿入することはできませんが，セルのフォントやフォーマットは自由に設定することができます。固定値のテキストは，直接，セルのデータに入力してください。変数（配列）やフィールド，4D言語のフォーミュラ式を挿入したいセルには，自分でそれと判別できるプレースホルダーを記入しておきます。（この例では```[Table_1]Field_1```というマークアップを使用しました。）繰り返されるデータ以外にも，リストの上下などにプレースホルダーを作成しても構いません。

![](https://github.com/4D-JP/export-xml-for-excel/blob/master/images/template.png)

最後に，このスプレッドシートをExcel 2004 XML スプレッドシート (.xml)」形式で保存します。

![](https://github.com/4D-JP/export-xml-for-excel/blob/master/images/saveas.png)

一部の機能性が失われるというアラートが表示されるかもしれませんが，ともかく保存してください。

![](https://github.com/4D-JP/export-xml-for-excel/blob/master/images/alert.png)

ここからは，少し慎重な作業が求められます。Excelで作成したオリジナルのXMLファイルのバックアップを事前に作成しておくと良いかもしれません。

XMLスプレッドシートファイルをテキストエディターで開いてみると，内容はUTF-8エンコーディングのXMLファイルであることが分かります。

このXMLを```PROCESS HTML TAGS```用のテンプレートとして使用するためには，データに連動するダイナミックな部分を4Dタグで置き換える必要があります。4Dタグは，XMLのコメント文なので，このファイルをテキストエディターで開く限り，失われることはありません。しかし，Excelでこのファイルを再び開いて保存すると，4Dタグがすべて取り除かれ，もとのスタティックなXMLファイルに戻ってしまうので注意してください。つまり，テンプレートとして使用するファイルは，以後，Excelでは開けないということです。

```xml
<?xml version="1.0"?>
<Workbook xmlns="urn:schemas-microsoft-com:office:spreadsheet"
 xmlns:o="urn:schemas-microsoft-com:office:office"
 xmlns:x="urn:schemas-microsoft-com:office:excel"
 xmlns:ss="urn:schemas-microsoft-com:office:spreadsheet"
 xmlns:html="http://www.w3.org/TR/REC-html40">
 <DocumentProperties xmlns="urn:schemas-microsoft-com:office:office">
  <Author>miyako keisuke</Author>
  <LastAuthor>miyako keisuke</LastAuthor>
  <Created>2012-06-11T02:02:53Z</Created>
  <LastSaved>2012-06-11T04:27:55Z</LastSaved>
  <Company>4D</Company>
  <Version>14.0</Version>
 </DocumentProperties>
 <OfficeDocumentSettings xmlns="urn:schemas-microsoft-com:office:office">
  <AllowPNG/>
 </OfficeDocumentSettings>
 <ExcelWorkbook xmlns="urn:schemas-microsoft-com:office:excel">
  <WindowHeight>4460</WindowHeight>
  <WindowWidth>18860</WindowWidth>
  <WindowTopX>1580</WindowTopX>
  <WindowTopY>0</WindowTopY>
  <ProtectStructure>False</ProtectStructure>
  <ProtectWindows>False</ProtectWindows>
 </ExcelWorkbook>
 <Styles>
  <Style ss:ID="Default" ss:Name="Normal">
   <Alignment ss:Vertical="Bottom"/>
   <Borders/>
   <Font ss:FontName="ＭＳ Ｐゴシック" x:CharSet="128" x:Family="Swiss" ss:Size="12"
    ss:Color="#000000"/>
   <Interior/>
   <NumberFormat/>
   <Protection/>
  </Style>
  <Style ss:ID="s62">
   <Font ss:FontName="ＭＳ Ｐゴシック" x:CharSet="128" ss:Size="12" ss:Color="#CCFFCC"/>
   <Interior ss:Color="#006411" ss:Pattern="Solid"/>
  </Style>
  <Style ss:ID="s63">
   <Alignment ss:Vertical="Center"/>
   <Font ss:FontName="ＭＳ Ｐゴシック" x:CharSet="128" ss:Size="18" ss:Color="#000000"/>
  </Style>
  <Style ss:ID="s64">
   <Alignment ss:Horizontal="Center" ss:Vertical="Center"/>
   <Font ss:FontName="ＭＳ Ｐゴシック" x:CharSet="128" x:Family="Swiss" ss:Size="12"
    ss:Color="#FFFFFF" ss:Bold="1"/>
   <Interior ss:Color="#000090" ss:Pattern="Solid"/>
  </Style>
  <Style ss:ID="s65">
   <Font ss:FontName="ＭＳ Ｐゴシック" x:CharSet="128" x:Family="Swiss" ss:Size="12"
    ss:Color="#000000"/>
   <Interior/>
  </Style>
  <Style ss:ID="s66">
   <Font ss:FontName="ＭＳ Ｐゴシック" x:CharSet="128" x:Family="Swiss" ss:Size="12"
    ss:Color="#000000"/>
   <Interior ss:Color="#CCFFCC" ss:Pattern="Solid"/>
  </Style>
 </Styles>
 <Worksheet ss:Name="Sheet1">
  <Table ss:ExpandedColumnCount="8" ss:ExpandedRowCount="9" x:FullColumns="1"
   x:FullRows="1" ss:DefaultColumnWidth="77" ss:DefaultRowHeight="18">
   <Column ss:AutoFitWidth="0" ss:Width="163"/>
   <Column ss:AutoFitWidth="0" ss:Width="136"/>
   <Column ss:Index="8" ss:AutoFitWidth="0" ss:Width="155"/>
   <Row ss:AutoFitHeight="0"/>
   <Row ss:AutoFitHeight="0">
    <Cell ss:StyleID="s62"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">シュツリョクビ</PhoneticText><Data
      ss:Type="String">出力日:</Data></Cell>
    <Cell ss:StyleID="s62"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">ヒヅケ</PhoneticText><Data
      ss:Type="String">^^^日付$$$</Data></Cell>
   </Row>
   <Row ss:Index="4" ss:AutoFitHeight="0" ss:StyleID="s63">
    <Cell ss:StyleID="s64"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">モジレツ</PhoneticText><Data
      ss:Type="String">文字列</Data></Cell>
    <Cell ss:StyleID="s64"><Data ss:Type="String">テキスト</Data></Cell>
    <Cell ss:StyleID="s64"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">ヒヅケ</PhoneticText><Data
      ss:Type="String">日付</Data></Cell>
    <Cell ss:StyleID="s64"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">ジカン</PhoneticText><Data
      ss:Type="String">時間</Data></Cell>
    <Cell ss:StyleID="s64"><Data ss:Type="String">ブール</Data></Cell>
    <Cell ss:StyleID="s64"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">セイスウ</PhoneticText><Data
      ss:Type="String">整数</Data></Cell>
    <Cell ss:StyleID="s64"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">バイチョウセイスウ</PhoneticText><Data
      ss:Type="String">倍長整数</Data></Cell>
    <Cell ss:StyleID="s64"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">ジッスウ</PhoneticText><Data
      ss:Type="String">実数</Data></Cell>
   </Row>
   <Row ss:AutoFitHeight="0">
    <Cell ss:StyleID="s65"><Data ss:Type="String">^^^[Table_1]Field_1$$$</Data></Cell>
    <Cell ss:StyleID="s65"><Data ss:Type="String">^^^[Table_1]Field_2$$$</Data></Cell>
    <Cell ss:StyleID="s65"><Data ss:Type="String">^^^[Table_1]Field_3$$$</Data></Cell>
    <Cell ss:StyleID="s65"><Data ss:Type="String">^^^[Table_1]Field_4$$$</Data></Cell>
    <Cell ss:StyleID="s65"><Data ss:Type="String">^^^[Table_1]Field_5$$$</Data></Cell>
    <Cell ss:StyleID="s65"><Data ss:Type="String">^^^[Table_1]Field_6$$$</Data></Cell>
    <Cell ss:StyleID="s65"><Data ss:Type="String">^^^[Table_1]Field_7$$$</Data></Cell>
    <Cell ss:StyleID="s65"><Data ss:Type="String">^^^[Table_1]Field_8$$$</Data></Cell>
   </Row>
   <Row ss:AutoFitHeight="0">
    <Cell ss:StyleID="s66"><Data ss:Type="String">^^^[Table_1]Field_1$$$</Data></Cell>
    <Cell ss:StyleID="s66"><Data ss:Type="String">^^^[Table_1]Field_2$$$</Data></Cell>
    <Cell ss:StyleID="s66"><Data ss:Type="String">^^^[Table_1]Field_3$$$</Data></Cell>
    <Cell ss:StyleID="s66"><Data ss:Type="String">^^^[Table_1]Field_4$$$</Data></Cell>
    <Cell ss:StyleID="s66"><Data ss:Type="String">^^^[Table_1]Field_5$$$</Data></Cell>
    <Cell ss:StyleID="s66"><Data ss:Type="String">^^^[Table_1]Field_6$$$</Data></Cell>
    <Cell ss:StyleID="s66"><Data ss:Type="String">^^^[Table_1]Field_7$$$</Data></Cell>
    <Cell ss:StyleID="s66"><Data ss:Type="String">^^^[Table_1]Field_8$$$</Data></Cell>
   </Row>
   <Row ss:Index="9" ss:AutoFitHeight="0">
    <Cell ss:Index="7" ss:StyleID="s62"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">ケンスウ</PhoneticText><Data
      ss:Type="String">件数:</Data></Cell>
    <Cell ss:StyleID="s62"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">ケンスウ</PhoneticText><Data
      ss:Type="String">^^^件数$$$</Data></Cell>
   </Row>
  </Table>
  <WorksheetOptions xmlns="urn:schemas-microsoft-com:office:excel">
   <PageSetup>
    <Header x:Margin="0.3"/>
    <Footer x:Margin="0.3"/>
    <PageMargins x:Bottom="0.75" x:Left="0.7" x:Right="0.7" x:Top="0.75"/>
   </PageSetup>
   <Unsynced/>
   <Print>
    <ValidPrinterInfo/>
    <PaperSizeIndex>9</PaperSizeIndex>
    <HorizontalResolution>-4</HorizontalResolution>
    <VerticalResolution>-4</VerticalResolution>
   </Print>
   <PageLayoutZoom>0</PageLayoutZoom>
   <Selected/>
   <Panes>
    <Pane>
     <Number>3</Number>
     <ActiveRow>14</ActiveRow>
     <ActiveCol>7</ActiveCol>
    </Pane>
   </Panes>
   <ProtectObjects>False</ProtectObjects>
   <ProtectScenarios>False</ProtectScenarios>
  </WorksheetOptions>
 </Worksheet>
</Workbook> 
```

はじめに，「差し込み」データのプレースホルダーの文字列を検索して```<!--!#4DTEXT-->```タグで置き換えましょう。```4DTEXT```は，```4DVAR```に代わり，v12.2で導入された新しい4Dタグです。v12では，どちらのタグも使用できますが，よりセキュリティの強化された```4DTEXT```が推奨されています。v13であれば，```4DTEXT```を使用してください。

[http://doc.4d.com/4D-12.4/Web/4D-HTML.300-977162.ja.html](http://doc.4d.com/4D-12.4/Web/4D-HTML.300-977162.ja.html)

今回の例では```^^^[Table_1]Field_1$$$```というマークアップを使用したので，```^^^```を```<!--!#4DTEXT ```で置換し，```$$$```を```-->```で置換すれば良い，ということになります。

4Dタグは，フィールド，変数，配列だけでなく，フォーミュラーも参照することができます。たとえば，「出力日:」の右隣に作成した```^^^```日付```$$$```」というプレースホルダーは```<!--!#4DTEXT 日付-->```というタグに置換し，「日付」というプロセス変数を代入させることもできますが，```<!--!#4DTEXT String(Current date;ISO Date GMT)-->```というフォーミュラーにすれば，よりダイナミックなテンプレートに仕立てることができます。```^^^```件数```$$$```」も同様で，```<!--!#4DTEXT Records in selection([テーブル名])-->```のようなフォーミュラーがそのまま使用できます。

セレクションのレコードが繰り返されるべき部分は，最初と最後にそれぞれ```<!--!#4DLOOP [テーブル名]または配列名-->```および```<!--!#4DENDLOOP-->```を挿入します。さらに，偶数行と奇数行でスタイルを切り替えたいので，```<!--!#4DIF (Selected record number([テーブル名])%2=1)-->```，```<!--!#4DELSE-->```，```<!--!#4DENDIF-->```でその分岐条件を記述します。

データの「差し込み」に関わる部分は以上ですが，このままでは，```PROCESS HTML TAGS``` で「差し込み」の処理することができません。テーブル構造ななどのドキュメント情報は，Excelで出力したオリジナルの文書が残されているためです。

XMLスプレッドシートのWorksheet/Table要素には，ss:ExpandedColumnCount属性およびss:ExpandedRowCount属性があり，それぞれ有効な列数および行数に対応しています。欠落している場合，Excelは自動的にその情報を補うため，手っ取り早いのは，これを削除してしまうことです。

```xml
 <Worksheet ss:Name="Sheet1">
  <Table ss:ExpandedColumnCount="8" ss:ExpandedRowCount="9" x:FullColumns="1"
   x:FullRows="1" ss:DefaultColumnWidth="77" ss:DefaultRowHeight="18">
```

もし，これも動的に書き込みのであれば，下記のようにタグを挿入することができます。この場合，```PROCESS HTML TAGS```は，正常に動作しますが，文書はXMLとしての妥当性を失うことになるので注意してください。（タグの入れ子）

```xml
 <Worksheet ss:Name="Sheet1">
  <Table ss:ExpandedColumnCount="8" ss:ExpandedRowCount="<!--#4DTEXT (Records in selection([Table_1])+9)-->" 
x:FullColumns="1"  x:FullRows="1" ss:DefaultColumnWidth="77" ss:DefaultRowHeight="18">
```

```xml
 <Worksheet ss:Name="Sheet1">
  <Table ss:ExpandedColumnCount="8" ss:ExpandedRowCount="<!--#4DTEXT (Records in selection([Table_1])+9)-->" 
x:FullColumns="1"  x:FullRows="1" ss:DefaultColumnWidth="77" ss:DefaultRowHeight="18">
```

XMLスプレッドシートには，データのない行列を省略し，データが再開する行列がインデックスで指定されるようになっています。つまり，1-5行目にはデータが存在し，6，7行は空白，8行目にはデータが存在するような場合，1-5行目はそれぞれ<row>要素，6，7行目は省略，8行目は<row ss:Index="8">という要素になり，その位置情報が属性で与えられます。Excelで作成したテンプレートの場合，レコードが繰り返されるセクションより上位のインデックスは問題ありませんが，データが繰り返されるセクションの下位のインデックスは，レコード数だけインクリメントされなければならない，ということです。ここでもやはり，省略するか（その場合，空白の行が発生しないようにデザインする必要があります），タグの入れ子で属性値を制御してください。

```xml
   <!--#4DENDIF-->  
   <!--#4DENDLOOP-->  
   <Row ss:Index="<!--#4DTEXT (Records in selection([Table_1])+9)-->" ss:AutoFitHeight="0">
```

最終的には，下記のようなファイルが出来上がります。

```xml
<?xml version="1.0"?>
<Workbook xmlns="urn:schemas-microsoft-com:office:spreadsheet"
 xmlns:o="urn:schemas-microsoft-com:office:office"
 xmlns:x="urn:schemas-microsoft-com:office:excel"
 xmlns:ss="urn:schemas-microsoft-com:office:spreadsheet"
 xmlns:html="http://www.w3.org/TR/REC-html40">
 <DocumentProperties xmlns="urn:schemas-microsoft-com:office:office">
  <Author>miyako keisuke</Author>
  <LastAuthor>miyako keisuke</LastAuthor>
  <Created>2012-06-11T02:02:53Z</Created>
  <LastSaved>2012-06-11T04:27:55Z</LastSaved>
  <Company>4D</Company>
  <Version>14.0</Version>
 </DocumentProperties>
 <OfficeDocumentSettings xmlns="urn:schemas-microsoft-com:office:office">
  <AllowPNG/>
 </OfficeDocumentSettings>
 <ExcelWorkbook xmlns="urn:schemas-microsoft-com:office:excel">
  <WindowHeight>4460</WindowHeight>
  <WindowWidth>18860</WindowWidth>
  <WindowTopX>1580</WindowTopX>
  <WindowTopY>0</WindowTopY>
  <ProtectStructure>False</ProtectStructure>
  <ProtectWindows>False</ProtectWindows>
 </ExcelWorkbook>
 <Styles>
  <Style ss:ID="Default" ss:Name="Normal">
   <Alignment ss:Vertical="Bottom"/>
   <Borders/>
   <Font ss:FontName="ＭＳ Ｐゴシック" x:CharSet="128" x:Family="Swiss" ss:Size="12"
    ss:Color="#000000"/>
   <Interior/>
   <NumberFormat/>
   <Protection/>
  </Style>
  <Style ss:ID="s62">
   <Font ss:FontName="ＭＳ Ｐゴシック" x:CharSet="128" ss:Size="12" ss:Color="#CCFFCC"/>
   <Interior ss:Color="#006411" ss:Pattern="Solid"/>
  </Style>
  <Style ss:ID="s63">
   <Alignment ss:Vertical="Center"/>
   <Font ss:FontName="ＭＳ Ｐゴシック" x:CharSet="128" ss:Size="18" ss:Color="#000000"/>
  </Style>
  <Style ss:ID="s64">
   <Alignment ss:Horizontal="Center" ss:Vertical="Center"/>
   <Font ss:FontName="ＭＳ Ｐゴシック" x:CharSet="128" x:Family="Swiss" ss:Size="12"
    ss:Color="#FFFFFF" ss:Bold="1"/>
   <Interior ss:Color="#000090" ss:Pattern="Solid"/>
  </Style>
  <Style ss:ID="s65">
   <Font ss:FontName="ＭＳ Ｐゴシック" x:CharSet="128" x:Family="Swiss" ss:Size="12"
    ss:Color="#000000"/>
   <Interior/>
  </Style>
  <Style ss:ID="s66">
   <Font ss:FontName="ＭＳ Ｐゴシック" x:CharSet="128" x:Family="Swiss" ss:Size="12"
    ss:Color="#000000"/>
   <Interior ss:Color="#CCFFCC" ss:Pattern="Solid"/>
  </Style>
 </Styles>
 <Worksheet ss:Name="Sheet1">
  <Table x:FullColumns="1" ss:ExpandedColumnCount="8" 
   ss:ExpandedRowCount="<!--#4DTEXT (Records in selection([Table_1])+9)-->"
   x:FullRows="1" ss:DefaultColumnWidth="77" ss:DefaultRowHeight="18">
   <Column ss:AutoFitWidth="0" ss:Width="163"/>
   <Column ss:AutoFitWidth="0" ss:Width="136"/>
   <Column ss:Index="8" ss:AutoFitWidth="0" ss:Width="155"/>
   <Row ss:AutoFitHeight="0"/>
   <Row ss:AutoFitHeight="0">
    <Cell ss:StyleID="s62"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">シュツリョクビ</PhoneticText><Data
      ss:Type="String">出力日:</Data></Cell>
    <Cell ss:StyleID="s62"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">ヒヅケ</PhoneticText><Data
      ss:Type="String"><!--#4DTEXT String(Current date;ISO Date GMT)--></Data></Cell>
   </Row>
   <Row ss:Index="4" ss:AutoFitHeight="0" ss:StyleID="s63">
    <Cell ss:StyleID="s64"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">モジレツ</PhoneticText><Data
      ss:Type="String">文字列</Data></Cell>
    <Cell ss:StyleID="s64"><Data ss:Type="String">テキスト</Data></Cell>
    <Cell ss:StyleID="s64"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">ヒヅケ</PhoneticText><Data
      ss:Type="String">日付</Data></Cell>
    <Cell ss:StyleID="s64"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">ジカン</PhoneticText><Data
      ss:Type="String">時間</Data></Cell>
    <Cell ss:StyleID="s64"><Data ss:Type="String">ブール</Data></Cell>
    <Cell ss:StyleID="s64"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">セイスウ</PhoneticText><Data
      ss:Type="String">整数</Data></Cell>
    <Cell ss:StyleID="s64"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">バイチョウセイスウ</PhoneticText><Data
      ss:Type="String">倍長整数</Data></Cell>
    <Cell ss:StyleID="s64"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">ジッスウ</PhoneticText><Data
      ss:Type="String">実数</Data></Cell>
   </Row>
   <!--#4DLOOP [Table_1]-->
   <!--#4DIF (Selected record number([Table_1])%2=1)-->     
   <Row ss:AutoFitHeight="0">
    <Cell ss:StyleID="s65"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_1--></Data></Cell>
    <Cell ss:StyleID="s65"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_2--></Data></Cell>
    <Cell ss:StyleID="s65"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_3--></Data></Cell>
    <Cell ss:StyleID="s65"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_4--></Data></Cell>
    <Cell ss:StyleID="s65"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_5--></Data></Cell>
    <Cell ss:StyleID="s65"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_6--></Data></Cell>
    <Cell ss:StyleID="s65"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_7--></Data></Cell>
    <Cell ss:StyleID="s65"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_8--></Data></Cell>
   </Row>
   <!--#4DELSEIF-->     
   <Row ss:AutoFitHeight="0">
    <Cell ss:StyleID="s66"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_1--></Data></Cell>
    <Cell ss:StyleID="s66"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_2--></Data></Cell>
    <Cell ss:StyleID="s66"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_3--></Data></Cell>
    <Cell ss:StyleID="s66"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_4--></Data></Cell>
    <Cell ss:StyleID="s66"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_5--></Data></Cell>
    <Cell ss:StyleID="s66"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_6--></Data></Cell>
    <Cell ss:StyleID="s66"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_7--></Data></Cell>
    <Cell ss:StyleID="s66"><Data ss:Type="String"><!--#4DTEXT [Table_1]Field_8--></Data></Cell>
   </Row>
   <!--#4DENDIF-->  
   <!--#4DENDLOOP-->    
   <Row ss:Index="<!--#4DTEXT (Records in selection([Table_1])+9)-->" ss:AutoFitHeight="0">
    <Cell ss:Index="7" ss:StyleID="s62"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">ケンスウ</PhoneticText><Data
      ss:Type="String">件数:</Data></Cell>
    <Cell ss:StyleID="s62"><PhoneticText
      xmlns="urn:schemas-microsoft-com:office:excel">ケンスウ</PhoneticText><Data
      ss:Type="String"><!--#4DTEXT Records in selection([Table_1])--></Data></Cell>
   </Row>
  </Table>
  <WorksheetOptions xmlns="urn:schemas-microsoft-com:office:excel">
   <PageSetup>
    <Header x:Margin="0.3"/>
    <Footer x:Margin="0.3"/>
    <PageMargins x:Bottom="0.75" x:Left="0.7" x:Right="0.7" x:Top="0.75"/>
   </PageSetup>
   <Unsynced/>
   <Print>
    <ValidPrinterInfo/>
    <PaperSizeIndex>9</PaperSizeIndex>
    <HorizontalResolution>-4</HorizontalResolution>
    <VerticalResolution>-4</VerticalResolution>
   </Print>
   <PageLayoutZoom>0</PageLayoutZoom>
   <Selected/>
   <Panes>
    <Pane>
     <Number>3</Number>
     <ActiveRow>14</ActiveRow>
     <ActiveCol>7</ActiveCol>
    </Pane>
   </Panes>
   <ProtectObjects>False</ProtectObjects>
   <ProtectScenarios>False</ProtectScenarios>
  </WorksheetOptions>
 </Worksheet>
</Workbook>
```

テンプレートさえ出来れてしまえば，あとはTEXT変数に読み込んでPROCESS HTML TAGSを実行するだけでデータの挿入されたXMLスプレッドシートを出力することができます。

```
ALL RECORDS([Table_1])
REDUCE SELECTION([Table_1];100)

$filePath:=Get 4D folder(Current Resources folder)+"template.4dxml"
DOCUMENT TO BLOB($filePath;$fileData)
$fileText:=Convert to text($fileData;"utf-8")

PROCESS HTML TAGS($fileText;$fileText)

CONVERT FROM TEXT($fileText;"utf-8";$fileData)
$filePath:=System folder(Desktop)+"result.xml"
BLOB TO DOCUMENT($filePath;$fileData)
```

![](https://github.com/4D-JP/export-xml-for-excel/blob/master/images/win.png)

Mac OSの場合，ファイルタイプやファイルクリエーターを設定しても良いでしょう。

```
C_TEXT($1)

PLATFORM PROPERTIES($platform)

If ($platform=Mac OS)

$filePath:=$1

If (Test path name($filePath)=Is a document)

SET DOCUMENT CREATOR($filePath;"XCEL")
SET DOCUMENT TYPE($filePath;"XMLS")

End if 

End if
```

**応用編**

サンプルコードには，```LAUNCH EXTERNAL PROCESS```とスクリプト言語を使用し，XML文書をXLSスプレッドシートに変換したり，Excelを起動してファイルを開くメソッドが収録されています。

```
ALL RECORDS([Table_1])
REDUCE SELECTION([Table_1];100)

$filePath:=Get 4D folder(Current Resources folder)+"template.4dxml"

$fileText:=XML_Read_document ($filePath)

PROCESS HTML TAGS($fileText;$fileText)

If (Shift down)`XLS

$filePath:=Temporary folder+Generate UUID+".xml"
$xlsPath:=System folder(Desktop)+Generate UUID+".xls"

XML_WRITE_DOCUMENT ($fileText;$filePath)
XML_SAVE_AS_XLS ($filePath;$xlsPath)
XML_OPEN_DOCUMENT ($xlsPath)

Else `XML

$filePath:=System folder(Desktop)+Generate UUID+".xml"

XML_WRITE_DOCUMENT ($fileText;$filePath)
XML_SET_DOCUMENT_TYPE ($filePath)
XML_OPEN_DOCUMENT ($filePath)

End if 
```
__例題__

* [export-xml-for-excel](https://github.com/4D-JP/export-xml-for-excel)
