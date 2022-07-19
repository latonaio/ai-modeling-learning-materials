# Jetsonへのデプロイ
実際Jetsonへのデプロイを行う手順です。Jetsonのセットアップ、Engineの生成、カメラの接続、DeepStreamによる推論の四段階で行います。

### Jetsonのセットアップ
Nvidia SDK Managerを使用して、Jetpackをインストールします。

### Engineの生成 
tao-toolkitの機能を使用します。

### 例
動作環境のディレクトリ構成
    ```
    .
    ├── tao-converter
    ├── resnet18_peoplenet_pruned.etlt
    └── peoplenet.engine (生成物)
    ```

- command
    ```sh
    tao-converter -k tlt_encode -t fp16 -d 3,544,960 -e peoplenet.engine resnet18_peoplenet_pruned.etlt
    ```

- option
    | オプション | 説明 |
    | --- | --- |
    | -k | トレーニング時、`tlt`モデルにエンコードする際に使用されるキー |
    | -t | エンジンのデータ型。fp32, fp16, int8から選択する。 |
    | -d | `export`の際に使用する次元のカンマ区切りリスト |
    | -e | Engineファイルを保存するパス |
    |   |   |

### カメラの接続
JetsonにUSBカメラを接続します,`/dev/video0`が存在していることを確認します。

< Deepstreamによる推論 > <br>
- 物体検出の様子は画面に出力され、mp4ファイルとして保存されます。
- 動作環境のディレクトリ構成
```
.
├── peoplenet.engine
├── deepstream_app_source1_peoplenet.txt
├── config_infer_primary_peoplenet.txt
├── config_tracker_NvDCF_perf.yml
├── labels_peoplenet.txt
└── out.mp4 (生成物)
```

- command
    ```sh
    deepstream-app -c deepstream_app_source1_peoplenet.txt
    ```

- option
    | オプション | 説明 |
    | --- | --- |
    | -c | 設定ファイルのパス |
    |   |   |

### ラトナのレポジトリを使用したDocker内での実装
ラトナではDocker内での実装を行うためのレポジトリの一つとして、[peoplenet-on-tao-tool-kit](https://github.com/latonaio/peoplenet-on-tao-toolkit)を用意しています。  
ラトナのレポジトリを使用することで、Docker内でEngineの生成が可能です。