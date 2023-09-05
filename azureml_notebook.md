## Azure Machine Learning ノートブックを使用する場合

Azure Virtual Machine を使ったノートブック環境の代わりに、Azure Machine Learning notebook (ノートブック) を使用する場合には、実行環境を以下の通りセットアップしてください。

[Azure Portal](https://portal.azure.com) 上で Azure Machine Learning のリソースを新規作成します。

作成されたら、[Azure Machine Learning studio](https://ml.azure.com/) を表示します。

ナビゲーションメニューから「ノートブック」を選択し、```Users/{ユーザーフォルダ}``` の下で、「Create new file」を選択してシェルスクリプト (.sh) を作成します。<br>
(ファイル名の拡張子を「.sh」として、ファイルタイプも「Bash (*.sh)」に変更してください。)

![スクリプトの新規作成](https://learn.microsoft.com/en-us/azure/machine-learning/media/how-to-create-manage-compute-instance/create-or-upload-file.png)

作成したファイルに、下記の通り記述して Save します。<br>
```{RESOURCE NAME}``` には、[こちら](./Readme.md) で作成した Azure OpenAI Service リソースのリソース名を設定し、```{API KEY}``` には取得した API キーを設定します。

```
#!/bin/bash

set -e

sudo -u azureuser -i <<'EOF'
# install packages
conda activate azureml_py38
pip install openai
pip install datasets tiktoken matplotlib plotly
conda deactivate
# set environment variables
echo "os.environ['OPENAI_API_TYPE']='azure'" | tee -a $HOME/.ipython/profile_default/startup/10_setup.py >/dev/null
echo "os.environ['OPENAI_API_VERSION']='2023-05-15'" | tee -a $HOME/.ipython/profile_default/startup/10_setup.py >/dev/null
echo "os.environ['OPENAI_API_BASE']='https://{RESOURCE NAME}.openai.azure.com'" | tee -a $HOME/.ipython/profile_default/startup/10_setup.py >/dev/null
echo "os.environ['OPENAI_API_KEY']='{API KEY}'" | tee -a $HOME/.ipython/profile_default/startup/10_setup.py >/dev/null
EOF
```

> Note : これらの設定が正しく反映されたどうかを確認する方法については下記 (一番下) の Note を参照してください。<br>
> Azure Machine Learning コンピュートインスタンス用のセットアップ スクリプトのサンプルについては、[こちら](https://github.com/azure/azureml-examples/tree/main/setup/setup-ci) を参照してください。


ナビゲーションメニューから「コンピュート」を選択し、「コンピュートインスタンス」を新規作成します。<br>
この際、「詳細設定」 (Advanced Settings) で、「Provision with setup script」を有効にして、creation script として上記で作成したスクリプトを設定して新規作成します。

コンピュートインスタンスが起動したら、下記手順でコードを記述します。<br>
まず、ナビゲーションメニューから「ノートブック」を選択し、ターミナル (コンソール) を表示します。

![ターミナルの表示](https://learn.microsoft.com/en-us/azure/machine-learning/media/how-to-use-terminal/open-terminal-window.png)

下記を入力して、この GitHub リポジトリをコピー (クローン) します。

```
git clone https://github.com/tsmatsuz/llm-grounding
```

コピーされたリポジトリ内のノートブック (.ipynb) を開き、カーネルを [Python 3.8 - Azure ML] に変更して、ノートブック上の指示に従って順番に実行します。

<blockquote>
Note : ノートブック環境で OpenAI Python Library のパッケージが使用可能か否かは、下記で確認してください。

```
import openai
```

また、環境変数が正しく設定されているかどうかは、下記で確認してください。

```
import os
os.environ["OPENAI_API_TYPE"]
```
</blockquote>
