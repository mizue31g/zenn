---
title: "AlphaFoldってなに？"
emoji: "😽"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [gcp,googlecloud,alphafold,lifesciences,machinelearning]
published: false
---

こんにちは。Google Cloudのカスタマーエンジニアの水江です。
この記事は [Google Cloud Japan Advent Calendar 2022](https://zenn.dev/google_cloud_jp/articles/12bd83cd5b3370) の 14 日目の記事です。

# TL;DR
もしあなたが生命科学分野の研究者であれば AlphaFold のことはもうご存知でしょう。でも、この記事を読んでいる大半の方は、おそらく自分の業務とは関係ないと思います。そのような方は、この記事のタイトルの通りAlphaFoldってなに？ という感じかもしれません。
ゲノムやタンパク質に関する知識がない方でも、この記事を読んだあとは AlphaFold のことを分かっ(た気になっ)て、そしてGoogle Cloud上でAlphaFoldを実行するための複数の方法があることと、それぞれの方法の特徴をなんとなく理解してもらうことを目標とします。
なお、AlphaFoldの詳細な実行手順については、他の記事へのリンクを添付していますのでそちらを参照してください。

# AlphaFold
すべての人が生涯を通じて健康に暮らせれば良いのですが、残念ながら人は病気にかかってしまいます。
**では、病気って何でしょうか？**
日本人で最も多い死因となっている病気は、がんです。日本人のうち、およそ[２人に１人はがん](https://www.kyoukaikenpo.or.jp/g5/cat450/sb4502/p024/)と診断されています。また、ここ数年流行している新型コロナウィルスも、世界中の人々にとって身近でかつ新しい病気になりました。
がんなどの病気は遺伝子の変異が原因で発生します。遺伝子はDNAに含まれていて、DNAはRNAに転写されタンパク質に翻訳[^1]されます。そして、人間の体はタンパク質で構成されています。したがって、病原体である遺伝子を含むタンパク質に対して何かしら働きかけることができれば、病気を治すことが可能かもしれませんね。

[^1]:[セントラルドグマ](https://ja.wikipedia.org/wiki/%E3%82%BB%E3%83%B3%E3%83%88%E3%83%A9%E3%83%AB%E3%83%89%E3%82%B0%E3%83%9E)

私達は通常病気を治すために薬を飲みます。
**では、薬って何でしょうか？**
ほとんどの薬の主な役割は、タンパク質にピッタリはまるような分子が合体して、そのタンパク質の働きを変えることによって病気を治すというものです。

つまり、新しい薬を作るときは、タンパク質の立体構造を知ることがとても重要なステップになります。これは従来から [X線結晶構造解析](https://ja.wikipedia.org/wiki/X%E7%B7%9A%E7%B5%90%E6%99%B6%E6%A7%8B%E9%80%A0%E8%A7%A3%E6%9E%90)のような手法が使われていますが、非常に大きな手間やコスト、そして数ヶ月から数年もの長い時間がかかるものです。

[AlphaFoldはDeepMindが開発したAIモデル](https://en.wikipedia.org/wiki/AlphaFold)で、この手間や金銭的・時間的コストを一気に短縮しました。このAIモデルにアミノ酸配列データを入力すると、そのアウトプットとしてタンパク質の立体構造データが出力されます。つまり、わずか数時間データを分析するだけで、タンパク質の立体構造を高い精度で予測できるようになり、その結果、新しい病気に対する新薬の開発が劇的に早められることが期待できます。

このため、AlphaFoldは生物化学における50年来の課題を解決したとか、AlphaFoldによって時代が変わった、などとも評されています。また最近は、[生命科学部門のブレイクスルー賞も受賞](https://www.nature.com/articles/d41586-022-02999-9)しました。

:::message
Googleはタンパク質に限らず、かなり以前からゲノムなどの生命科学分野における先進的な研究を行ってきました。例えば、[DeepVariant](https://github.com/google/deepvariant)はディープラーニングの技術を使ってゲノムデータの変異を解析するツールで、2017年にオープンソースとして公開しています。また、さらにその前の2014年には [Google Genomics](https://cloud.google.com/life-sciences)というクラウドサービスを提供しています (2019年に Cloud Life Sciences に名称変更しています)。その他にも、[Google Health](https://health.google/)のサイトで様々な取り組みを紹介しています。
:::

# AlphaFoldをGoogle Cloudで動かす方法

DeepMindとGoogleは、Google Cloud上で容易にかつ効率的にAlphaFold を実行するためのさまざまなソリューションを提供しています。
これまでに複数のブログ記事でその方法を紹介してきましたが、どの方法を使っても最終的にはAlphaFoldが予測したタンパク質の立体構造を格納するPDB(Protein Data Bank)ファイルが生成されます。PDBビューワー等を使うことで、以下の図のようなタンパク質の立体構造を可視化できます。
![alphafold](https://user-images.githubusercontent.com/53395942/206932350-d85bfdd5-01f5-4de2-8c4a-d15ef4d0c224.gif =400x)
*pdbファイルの表示例*

## Compute Engine上で実行
![compute_engine](https://user-images.githubusercontent.com/17425951/206981119-c491a767-7595-4aec-b397-271926fce79d.png =600x)
この方法は最も一般的です。Google Cloudの[Compute Engine](https://cloud.google.com/compute)で仮想サーバを立ち上げて、AlphaFoldをインストールして実行するという流れです。DeepMindが公開したGithub上に手順が公開されています。
この方法のメリットは、オンプレミスでもクラウドでもほぼ同じ手順で実行できるという点です。また、デメリットとしては、IaaSベースのソリューションですので、サーバのセットアップやメンテナンス等はほぼすべて自分自身で行う必要があるという点です。
具体的な操作手順については[Github](https://github.com/deepmind/alphafold)をご参照ください。
またUKの同僚が書いたこちらのブログ記事も参考までに。
https://medium.com/google-cloud/running-alphafold-on-google-cloud-compute-engine-86e4eb1bbeed


## Vertex AI Workbenchで実行
![workbench](https://user-images.githubusercontent.com/17425951/206981752-cd499f50-9938-4a07-9c54-c1e3adf058f9.png =500x)
この方法は、AlphaFoldを最も簡単に試せる手順です。公開されているサンプルのJupyter Notebookは [Collaboratory](https://colab.research.google.com/) で実行できます。ただし、化合物情報など企業固有のデータを使う場合は、よりセキュリティを考慮した環境が必須という要望があります。そのような場合は、Google Cloud の[Vertex AI Workbench](https://cloud.google.com/vertex-ai-workbench)を使うことができます。この手順は、Googleが提供しているVertex AI Workbench用のノートブックがGithub上に提供されているので、それを実行するだけで予測結果を得ることができるというものです。
メリットはやはりその容易さです。事前のセットアップ作業が最小限で済むので、手っ取り早くすぐに実行できます。デメリットは、簡易データベースを使っているため、予測精度がやや劣ってしまう可能性があるという点です。
具体的な手順については、こちらのブログ記事を参照してください。
https://cloud.google.com/blog/ja/products/ai-machine-learning/running-alphafold-on-vertexai?hl=ja


## Google Cloud Life Sciencesで実行
![lifesciences](https://user-images.githubusercontent.com/17425951/206982019-c7d7164a-6985-46d1-b203-5d36d6277a35.png =600x)
[Google Cloud Life Sciences](https://cloud.google.com/life-sciences) は、ゲノムなどの生命科学分野のデータを処理するための専用サービスです。Cromwell、NextflowやSnakemakeなど、ゲノムデータの変異解析で一般的に使われるワークフローツールをすべてマネージドで実行することが可能です。
この方法のメリットは、マネージドサービスなので仮想サーバの構築や設定やメンテナンスが一切不要であることです。処理が終わったら自動的にコンピュートリソースも削除されるので、コストも最適化できます。そして、フルサイズのデータベースを使うため、CollaboratoryやVertex AI Workbenchを使う方法よりも高い精度の予測結果が期待できます。一方デメリットとしては、最初にコンテナイメージを作る必要があるので、Dockerの知識がない方には若干ハードルが高いかもしれません。
具体的な手順はこちらのブログの記事を参照してください。なお、Googleはバッチ処理を簡単に実行するための[dsub](https://github.com/DataBiosphere/dsub)というコマンドラインツールをオープンソースで公開しています。このdsubを使って、Google Cloud Life Sicencesを呼び出しAlphaFoldを実行するような手順となっています。
https://medium.com/google-cloud-jp/alphafold-with-google-cloud-life-sciences-67bacf2f91ed


## Vertex AI Pipelinesで実行
![vertex_ai_pirplienes](https://user-images.githubusercontent.com/17425951/206982400-03b8f0b4-85aa-407b-871b-2426d67520ee.png =650x)
[Vertex AI Pipelines](https://cloud.google.com/vertex-ai/docs/pipelines)を使ってAlphaFoldを実行する方法は、最も直近で発表されたソリューションです。DeepMindが公開したオリジナルのAlphaFold予測処理を分解し、それをさらに改善しました。
この方法のメリットは、リソースの利用効率を高め、速度も向上している点です。AlphaFoldの予測処理をざっくり２つに分類すると、前半はデータベースの検索処理、後半はAIを使った予測処理となります。前半ではGPUは不要なので、後半の予測処理にのみGPUを割り当てることでコストを削減できます。また、GUIでのパイプラインの可視化と共に、[Vertex ML Metadata](https://cloud.google.com/vertex-ai/docs/ml-metadata/introduction)により、実行履歴や中間成果物のトラッキングも可能です。一方デメリットは、Vertex AI に含まれる複数のマネージドサービスを使っているので、最初の学習コストがかかる可能性があります。
https://cloud.google.com/blog/ja/products/ai-machine-learning/alphafold-batch-inference-with-vertex-ai-pipelines?hl=ja


![Pipelines](https://user-images.githubusercontent.com/17425951/206982870-ce469053-a656-4875-bba8-4dfe1815669f.png =700x)
*Vertex AI Pipelinesによるパイプライン可視化の例*


# タンパク質構造データベース
ここまでは AlphaFold を実行する方法について複数のパターンを紹介しましたが、実はGoogle Cloudでは、AlphaFoldで予測した約2億個以上のタンパク質の構造情報をデータベース化し、誰でもがマーケットプレイスからアクセスできるようにしています。
データへのアクセスもとても簡単です。Google Cloud コンソール上で、マーケットプレイスを選択し、”AlphaFold” を検索すると以下の画面が表示されます。そこで、[データセットを表示]ボタンを押すだけです。

![ProteinDatabase](https://user-images.githubusercontent.com/17425951/206983223-4e9e8443-b517-4b6e-b2d5-a52dfbe92d2d.png =500x)

Google Cloud StorageでPDBファイルが共有されていて、かつ、メタデータがBigQueryに格納されています。BigQuery上でメタデータを検索して、お目当てのPDBファイルを取得するというような使い方が可能です。例えば、Homo Sapienceであり、かつplDDT[^2]値が0.5より大きなデータのGCS上のパスを取得するには、以下のようなクエリを実行します。

[^2]:plDDT(predicted local distance difference test)は予測の信頼度を表す数値

![query](https://user-images.githubusercontent.com/17425951/206984754-74a99430-db26-4cca-87f2-7590ac95d43b.png =500x)

# 最後に
AlphaFoldのような革新的な技術を開発し、世界中の人々がアクセスできて使えるようにすることはGoogleのミッションです。
今後もさらに画期的な技術やより良い手法がGoogleから出てくると思いますので、引き続きアップデートを期待してください。

今年も残り半月ほどですが、みなさまよい年末を迎えられますように。

明日は OpenTelemetry Collector に関する記事です！お楽しみに〜〜