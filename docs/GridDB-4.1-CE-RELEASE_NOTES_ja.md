# GridDB CE 4.1

## 変更点

GridDB CE V4.1の主な変更点は以下のとおりです。

1. オンライン増設
    - 稼働中のクラスタに対して、無停止でノード増設やノード切り離しを行うことができます。
2. 空間型
    - データ型として空間型が追加されました。また空間索引も利用できます。
3. 時系列圧縮
    - 時系列データに適した圧縮を時系列コンテナで利用できます。

4. カラム数の上限値の拡大
    - コンテナで扱えるカラム数の上限値を拡大しました。従来はカラム数の上限値は1024まででしたが、V4.1からは上限値は1024～32,000個になります。(ブロックサイズの設定やコンテナのカラム型による)
5. 動的なスキーマ変更(カラム追加)の改善
    - コンテナの末尾へのカラム追加において、カラム追加処理中のコンテナへの同時アクセスが可能になります。V4.1より前のバージョンではコンテナのスキーマ変更処理中に、他から同じコンテナにはアクセスできませんでした。また、末尾へのカラム追加の処理を高速化しました。

---

### オンライン増設

クラスタ運用中にメンテナンス等のために、オンラインでノード切り離したり、メンテナンス完了後に組込む操作ができます。さらには、システムを増強するためにノードを追加することもオンラインでできます。 

運用コマンドのgs_appendclusterやgs_leaveclusterなどを使って操作を行います。

### 空間(GEOMETRY)型

空間型のデータは地図情報システムなどでよく利用されています。

GEOMETRY型には、WKT（Well-known text）を用いてデータを記述します。WKTは、地理空間に関する情報の標準化などを推進している非営利団体OGC(Open Geospatial Consortium)にて策定されています。GridDBでは、コンテナのカラムをGEOMETRY型に設定することで、WKTで記述された空間情報をカラムに格納できます。 

GEOMETRY型には以下のWKT形式のデータを登録できます。 
- POINT 
    * 2次元または3次元の座標により生成される点。 
    * 記述例： POINT(0 10 10) 
- LINESTRING 
    * 2つ以上の点により表現される、2次元または3次元空間上の直線の集合。 
    * 記述例： LINESTRING(0 10 10, 10 10 10, 10 10 0) 
- POLYGON 
    * 直線の集合により表現される、2次元または3次元空間上の閉じた領域。POLYGONの頂点は反時計回りに指定します。POLYGON内に島をつくる場合、内部の点は時計回りで指定します。 
    * 記述例： POLYGON((0 0,10 0,10 10,0 10,0 0))、POLYGON ((35 10, 45 45, 15 40, 10 20, 35 10),(20 30, 35 35, 30 20, 20 30)) 
- POLYHEDRALSURFACE 
    * 2次元または3次元の座標により生成される点 
    * 記述例： POLYHEDRALSURFACE (((0 0 0, 0 1 0, 1 1 0, 1 0 0, 0 0 0)), ((0 0 0, 0 1 0, 0 1 1, 0 0 1, 0 0 0)),((0 0 0, 1 0 0, 1 0 1, 0 0 1, 0 0 0)), ((1 1 1, 1 0 1, 0 0 1, 0 1 1, 1 1 1)),((1 1 1, 1 0 1, 1 0 0, 1 1 0, 1 1 1)),((1 1 1, 1 1 0, 0 1 0, 0 1 1, 1 1 1)) ) 

GEOMETRY型を利用した演算は、APIやTQLで実行できます。 

TQLでは2次元、3次元の空間を定義する空間生成関数と空間型データ間での演算の関数を提供します。TQLではコンテナ内のGEOMETRY型のカラムと指定した空間データで演算を行いその結果を以下のようにして得ることができます。 

    SELECT * WHERE ST_MBRIntersects(geom, ST_GeomFromText('POLYGON((0 0,10 0,10 10,0 10,0 0))'))

### 時系列圧縮

時系列コンテナは、データを圧縮して保持することができます。圧縮オプションの指定は時系列コンテナ作成時に指定します。データを圧縮することでメモリ使用効率を上げることができます。 

圧縮オプションには次の指定ができます。 
- HI ：誤差あり間引き圧縮方式
- SS ：誤差なし間引き圧縮方式
- NO ：無圧縮

ただし、圧縮オプションが設定されている時系列コンテナに対しては、以下に示すロウ操作を行えません。 
- 指定ロウの更新 
- 指定ロウの削除 
- 指定時刻より新しい時刻のロウが存在する場合の、ロウの新規作成 

---

Copyright (C) 2019 TOSHIBA Digital Solutions Corporation
