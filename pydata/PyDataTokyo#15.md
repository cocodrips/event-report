# PyDataTokyo #15 分散処理の基礎の基礎 + PySpark勘所

## 分散処理本当に必要？
- CSVじゃなくて、圧縮をかけたまま読めるようなparquetのようなデータフォーマットを使う.
[CSV ファイルを Parquet ファイルに変換してクエリを高速化 - nagix](http://nagix.hatenablog.com/entry/2015/12/08/235512)
- メモリに収まるデータ？ 
  - アクセスにかかる時間の参考: [分散システムについて語らせてくれ](https://www.slideshare.net/kumagi/ss-78765920/4)
  - 1台のメモリに乗る量だったら、分散処理しなくてもいける

## 分散の処理で問題になること
- groupbyやsortのような処理
- メモリに収まらないデータ・セットはディスクアクセスを伴ってしまう

## クラウドサービス
- [Amazon EMR（Hadoop などのビッグデータフレームワークを簡単に実行）| AWS](https://aws.amazon.com/jp/emr/)
- [Databricks - Making Big Data Simple](https://databricks.com/)

## SparkとHadoopのエコシステム
https://twitter.com/cocodrips/status/912993339942739968

### (Py)Sparkの使い所
- スケールイン・アウトの幅が大きいのが大きな利点
- メモリに収まらないサイズのデータを扱うならsparkが有効
  - sparkは小規模データでも小回りがきく

### DataFrame API
- DataFrame APIは原則としてJVM内で処理される

## SparkとPyDataエコシステム
- ファイルのデータ交換
  - ファイルベースならParquetがお薦め (圧縮状態でPythonから読み書き可能)
  - インメモリの変換はApache Arrowに注目

### Parquet
- csv, jsonはCPUの負担が大きい
- Parquet -> Pythonで直接読み書き可能(pyarrowがよい/fastparquetってのもある)
- DataFrame APIではほぼ標準ファイルフォーマット扱い
- 分析的なタスクのデータソースとしてとても便利

- スキーマつきのテーブルデータを保存
  - zipでCSVを固めたときと同じくらいの圧縮率
  - 指定したフィールドだけ読み取ることでI/O削減
  - 更新には向かない
  - ランダムアクセスには向かない

- Pythonからの読み方: [Reading and Writing the Apache Parquet Format — pyarrow documentation](https://arrow.apache.org/docs/python/parquet.html)

### インメモリの変換
- pandas -> spark (めっちゃ遅い)
- インメモリ列思考データフォーマット: Apache Arrow

## tips
- pandasで扱うのはメモリの1/10くらいのサイズにしておこう 
- RDD APIをPythonから使うとめっちゃ遅い。DataFrame APIが早い
  - (Python <-> JVM でのシリアライズ・デシリアライズがめちゃくちゃ遅い)
