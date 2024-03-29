# Azure OpenAI Service による RAG (Retrieval Augmented Generation) ハンズオン

大規模言語モデル (Large Language Model, LLM) の RAG (Retrieval Augmented Generation) のアーキテクチャ (または Grounding) を API レベルで学ぶ目的のハンズオンです。

- [テキストの Embedding とデータベースへの保存](./01-embedding.ipynb)
- [質問応答 (Question-Answering) の実行 (演習)](./02-practice.ipynb)
- [質問応答 (Question-Answering) の実行 (回答例)](./03-answer.ipynb)

> プロンプトフロー (Prompt Flow) を用いた RAG の実装については、[こちら](https://github.com/tsmatsuz/prompt-flow-handson) のリポジトリを参照してください。

本ハンズオンを実施する前に、下記の準備をおこなってください。

## Azure OpenAI Service リソースの準備

[Azure Portal](https://portal.azure.com) にログインします。

Azure OpenAI Service のリソースを新規作成します。(既に作成済の方はスキップしてください。)<br>
gpt-35-turbo と text-embedding-ada-002 を使用しますので、どのリージョン (東日本含む) を選択しても構いません。

gpt-35-turbo と text-embedding-ada-002 を deploy します。(既に deploy 済の方はスキップしてください。)

Azure OpenAI Service の API キーを取得します。<br>
API キーは、リソースブレードの左メニューの「Keys and Endpoint」から取得できます。

> このハンズオンでは、大量ドキュメントの Embedding を実行します。text-embedding-ada-002 は、Tokens per minutes (TPM) の値を 120 K 以上に設定してください。

## 環境のセットアップ

今回は、以下の手順で、Microsoft Azure 上に Ubuntu の仮想マシンを作成し、ここでノートブックを使用します。

> Azure Machine Learning ノートブックを使用したセットアップ方法については、[azureml_notebook.md](./azureml_notebook.md) を参照してください。

まず、[Azure Portal](https://portal.azure.com) 上で Ubuntu Server 20.04 LTS のリソースを新規作成します。

作成されたら、ターミナル クライアント (PuTTY など) で Ubuntu にログインし、必要なライブラリ等のセットアップのため、コンソール上で以下を実行します。

```
sudo apt-get update
sudo apt-get install -y python3-pip
sudo -H pip3 install --upgrade pip
pip3 install openai
pip3 install datasets tiktoken matplotlib plotly
pip3 install jupyter
```

プロセス内で環境変数が設定されるよう、コンソール上で以下を実行します。<br>
なお、下記の { } 内は皆さんの環境にあわせて適切な値を設定してください。

```
echo -e "export OPENAI_API_TYPE=azure" >> ~/.bashrc
echo -e "export OPENAI_API_VERSION=2023-05-15" >> ~/.bashrc
echo -e "export OPENAI_API_BASE=https://{RESOURCE NAME}.openai.azure.com" >> ~/.bashrc
echo -e "export OPENAI_API_KEY={API KEY}" >> ~/.bashrc
```

> コマンド ```tail .bashrc``` で正しく設定されていることを確認してください。

## ノートブックの利用

いったんログアウトをおこない、ターミナル クライアント (PuTTY など) で再度ログインをおこないます。<br>
この際、ポート 8888 の Tunnel 設定 (port forwarding 設定) をおこなってください。

> PuTTY を使用した Tunnel 設定 (port forwarding 設定) については、[こちら](https://github.com/tsmatz/azureml-tutorial) の Readme.md を参照

コンソール上で、下記を実行します。

```
jupyter notebook
```

表示される URL (例: http://localhost:8888/tree?token=xxxxx) をコピーして、ブラウザーでアクセスします。

> Tunnel 設定 (port forwarding 設定) でうまく接続できない場合、Azure 仮想マシンのネットワーク (Networking) メニューを選択して、ポート (既定は 8888 を使用) を開けて接続してください。

コンソール上で、この GitHub リポジトリをコピー (クローン) して、各ノートブックを開いてください。

```
git clone https://github.com/tsmatsuz/llm-grounding
```

> このハンズオンは、gpt-35-turbo model version 0613、text-embedding-ada-002 model version 2 と Azure OpenAI Service api version 2023-05-15 で動作を確認しています。
