# AWSでの環境構築
実際にAWSでの環境構築を行う手順です。  
AWS EC2 instance 作成、インスタンスの初期設定、jupyter-note 起動の三段階で行います。  

### 使用するAMI
NVIDIA Deep Learningを使用します。  
アプリケーションおよび OS イメージ (Amazon マシンイメージ) の項目で、`その他のAMIを閲覧する` から選択できます。  

### 手順
1. AWS EC2 instance 作成
   1. AWS の管理画面から EC2 のページにアクセスする。
   2. `コンソールを起動` から新しいインスタンスを作成する。
       - distribution : NVIDIA Deep Learning AMI 
       - インスタンスタイプ：AWS の場合、p3系統以上 で GPUを使用可能
       - storage : 200GB 以上
2. インスタンスの初期設定
   - SSH や `EC2 Instance Connect` を使用して、立ち上げたインスタンスにアクセスする。
   -  apt update が機能しないので下記を実行
```
sudo su -
apt-key del 7fa2af80
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-keyring_1.0-1_all.deb .

rm /etc/apt/sources.list.d/cuda.list
rm /etc/apt/sources.list.d/nvidia-ml.list
```
   -  aws cli の設定
```
aws configure
```
の実行
- s3 等から学習データのダウンロード

3. jupyter-note 起動
nvidia公式を参考に jupyter-notebook 環境を構築する。<br>
https://docs.nvidia.com/tao/tao-toolkit/text/running_in_cloud/running_tao_toolkit_on_aws.html)

- jupyter-note 起動コマンド
```
nohup jupyter notebook --ip 0.0.0.0 --port 8888 --allow-root --NotebookApp.token='' &
```

### 参照
[NVIDIA公式サイト](https://docs.nvidia.com/tao/tao-toolkit/text/running_in_cloud/running_tao_toolkit_on_aws.html)