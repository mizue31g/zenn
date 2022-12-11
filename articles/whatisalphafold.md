---
title: "AlphaFoldってなに？"
emoji: "😽"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [gcp,googlecloud,alphafold,lifesciences]
published: false
---

# Titile : AlphaFoldってなに？

こんにちは。Google Cloudのカスタマーエンジニアの水江です。
この記事は [Google Cloud Japan Advent Calendar 2022](https://zenn.dev/google_cloud_jp/articles/12bd83cd5b3370) の 14 日目の記事です。

# 対象読者
もしあなたが生命科学分野の研究者であれば AlphaFold のことはもうご存知でしょう。でも、この記事を読んでいる大半の方は、おそらく自分の業務とは関係なさそうなので、この記事のタイトルの通りAlphaFoldってなに？ という感じかもしれません。
ゲノムやタンパク質に関する知識がない方でも、この記事を読んだあとは AlphaFold のことを分かっ(た気になっ)て、そしてGoogle Cloud上でAlphaFoldを実行できる複数の方法と、それぞれのメリット・デメリットをなんとなく理解してもらうことを目標とします。なお、AlphaFoldの具体的な実行方法は他の記事への参照リンクを記載しています。

# AlphaFold
すべての人が生涯を通じて健康に暮らせれば良いのですが、残念ながら人は病気にかかってしまいます。では、病気って何でしょうか？日本人で最も多い死因となっている病気は、がんです。日本人のうち、およそ２人に１人はがんと診断されています。また、ここ数年流行している新型コロナウィルスも、世界中の人々にとって身近でかつ新しい病気になりました。

がんなどの病気は遺伝子の変異が原因で発生します。遺伝子はDNAに含まれていて、DNAはRNAに転写されタンパク質に翻訳されます。そして、人間の体はタンパク質で構成されています。したがって、病原体である遺伝子を含むタンパク質に対して何かしら働きかけることができれば、病気を治すことが可能かもしれませんね。

私達は通常病気を治すために薬を飲みます。では、薬って何でしょうか？薬は、タンパク質にピッタリはまるような分子が合体して、そのタンパク質の働きを変えることによって病気を治すというものです。

つまり、新しい薬を作るときは、タンパク質の立体構造を知ることがとても重要なステップになります。これは従来から [X線結晶構造解析](https://ja.wikipedia.org/wiki/X%E7%B7%9A%E7%B5%90%E6%99%B6%E6%A7%8B%E9%80%A0%E8%A7%A3%E6%9E%90)のような手法が使われていますが、非常に手間やコスト、そして数ヶ月から数年もの長い時間がかかるものです。

[AlphaFoldは、DeepMindが開発したAIモデル](https://en.wikipedia.org/wiki/AlphaFold)です。 このAIモデルにアミノ酸配列データを入力すると、そのアウトプットとしてタンパク質の立体構造データが出力されます。つまり、わずか数時間データを分析するだけで、タンパク質の立体構造を高い精度で予測できるようになるのです。これがうまく使えるようなら、新しい病気に対する新薬の開発が劇的に早められることが期待できます。このため、AlphaFoldは生物化学の50年来の課題を解決したとか、AlphaFoldによって時代が変わった、などとも評されています。また、[生命科学部門のブレイクスルー賞も受賞](https://www.nature.com/articles/d41586-022-02999-9)しました。

:::message
Googleではタンパク質に限らず、かなり以前からゲノムなどの生命科学分野において先進的な研究を行ってきました。[DeepVariant](https://github.com/google/deepvariant)はディープラーニングの技術を使ってゲノムデータの変異を解析するツールで、2017年にオープンソースとして公開しています。また、さらにその前の2014年には [Google Genomics](https://cloud.google.com/life-sciences)というクラウドサービスを提供しています (2019年に Cloud Life Sciences に名称変更しています)。
:::

# AlphaFoldをGoogle Cloudで実行する方法

DeepMindとGoogleはGoogle Cloud上で容易にかつ効率的にAlphaFold を実行するソリューションを提供しています。
これまでに複数のブログサイト等でその方法を紹介していましたが、どの方法を使っても最終的にはAlphaFoldが予測したタンパク質の立体構造を格納したPDB(Protein Data Bank)ファイルが生成されます。PDBのビューワー等を使うことで、以下の図のようなタンパク質の立体構造を可視化できます。

## Compute Engine上で実行
これは最も一般的な方法です。Compute Engine(https://cloud.google.com/compute)で仮想サーバを立ち上げてAlphaFoldをインストールして実行するという流れです。Github上に手順が公開されています。
この方法のメリットは、オンプレミスでもクラウドでもほぼ同じ手順で実行できるという点です。また、デメリットとしては、IaaSベースのソリューションですので、サーバのセットアップやメンテナンス等をほぼすべて手動で行う必要があるという点です。
具体的な手順については[Github](https://github.com/deepmind/alphafold)をご参照ください。
また私のUKの同僚が書いたこちらのブログ記事も参考までに。[Running Alphafold on Google Cloud compute engine](https://medium.com/google-cloud/running-alphafold-on-google-cloud-compute-engine-86e4eb1bbeed)


## Vertex AI Workbenchで実行
AlphaFoldを最も簡単に試すには、Jupyter Notebook形式で公開されている [Colllaboratory](https://colab.research.google.com/) を使う方法があります。ただ、企業固有のデータを使うには、よりセキュリティを考慮した環境が必須という要望があります。そのような場合は、Google Cloud の[Vertex AI Workbench](https://cloud.google.com/vertex-ai-workbench)を使うことができます。この手順はユーザーマネージドノートブックが提供されているので、それを実行するだけで予測結果を得ることができるというものです。
メリットはやはりその容易さです。事前のセットアップ作業が最小限で済むので、すぐに実行できます。デメリットは、簡易データベースを使っているため予測精度がやや劣ってしまう可能性があるという点です。
具体的な手順については、こちらのブログ記事を参照してください。
[バイオファーマ企業、画期的なタンパク質フォールディング システム AlphaFold を Vertex AI で利用可能に](https://cloud.google.com/blog/ja/products/ai-machine-learning/running-alphafold-on-vertexai?hl=ja)


## Google Cloud Life Sciencesで実行
[Google Cloud Life Sciences](https://cloud.google.com/life-sciences) は、ゲノムなどの生命科学分野のデータ処理を行うための専用のサービスです。Cromwell、NextflowやSnakemakeなど、ゲノムデータの変異解析で一般的に使われるワークフローツールをすべてマネージドで実行することが可能です。
この方法のメリットは、マネージドサービスなので仮想サーバの構築や設定やメンテナンスが一切不要であることです。また、フルサイズのデータベースを使うため、CollaboratoryやVertex AI Workbenchを使ったソリューションよりも高い精度の予測結果が期待できます。一方、デメリットとしては、最初にコンテナイメージを作る必要があるので、Dockerの知識がない方には若干ハードルが高いかもしれません。
具体的な手順はこちらのブログ記事を参照してください。なお、Googleはバッチ処理を簡単に実行するためのdsubというコマンドラインツールをオープンソースで公開していますが、これはGoogle Cloud Life Sicencesを呼び出すことができるので、このdsubを使ってAlphaFoldを実行するような手順となっています。
[AlphaFold を Google Cloud Life Sciences で実行しよう](https://medium.com/google-cloud-jp/alphafold-with-google-cloud-life-sciences-67bacf2f91ed)



## Vertex AI Pipelinesで実行
[Vertex AI Pipelines](https://cloud.google.com/vertex-ai/docs/pipelines)を使ってAlphaFoldを実行する方法は、最も直近で発表されたソリューションです。DeepMindが公開したオリジナルのAlphaFold実行処理の中身を分解して、それをさらに改善しました。
メリットは、リソースの利用効率を高め、速度も向上しているという点です。必要な処理にのみGPUを割り当てて実行することでコスト的なメリットもあります。また、GUIでのパイプラインの可視化と共に、中間成果物のトラッキング等も可能です。デメリットは、Vertex AI に含まれる複数のマネージドサービスを使っているため、最初に学習コストがかかる可能性があります。
[AlphaFold のバッチ推論を Vertex AI Pipelines で実行する](https://cloud.google.com/blog/ja/products/ai-machine-learning/alphafold-batch-inference-with-vertex-ai-pipelines?hl=ja)



### Vertex AI Pipelinesによるパイプラインの可視化の例




# タンパク質構造データベース
ここまでは AlphaFold を実行する方法について複数のパターンを説明してきましたが、実はGoogle Cloudでは、AlphaFoldで予測した約2億個以上のタンパク質の構造情報をデータベース化し、誰でもがマーケットプレイスからアクセスできるようにしています。
データへのアクセスもとても簡単です。Google Cloud コンソール上で、マーケットプレイスを選択し、”AlphaFold” を検索すると以下の画面が表示されます。そこで、[データセットを表示]ボタンを押すだけです。
Google Cloud StorageでPDBファイルが共有されていて、かつ、メタデータがBigQueryに格納されています。BigQuery上でメタデータを検索して、お目当てのPDBファイルを取得するというような使い方が可能です。




# 最後に
AlphaFoldのような革新的な技術を開発し、世界中の人々がアクセスできて使えるようにすることはGoogleのミッションです。その結果として、人々の健康に貢献することができればそれは私達にとってこのうえない喜びです。
今後もさらに画期的な技術やより良い手法が出てくるかもしれませんので、引き続きGoogleからのアップデートを期待してください。

今年も残り 半月を切りましたが、みなさまよい年末を迎えられますように。

明日は XXX に関する記事です！お楽しみに〜

