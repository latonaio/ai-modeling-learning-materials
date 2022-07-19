# Kubernetesランタイム適用
### Kubernetesランタイム適用
Kubernetesランタイム適用は、AIや独自のコンテナを kubernetes 上で動作させるための方法です。  
kubernetesの立ち上げ、manifest file の準備、container の登録の三段階で行います。  

### kubernetes
Kubernetesは複数の container を一元管理するツールです。  
container の自動起動、container間同士の通信が可能です。

### kubernetesの立ち上げ
実際にkubernetesの立ち上げを行う手順です。  
インストール、起動、設定の読み込み、再起動の四段階で行います。  


### 手順
1. install list - apt で可能です
    - kubelet
    - kubeadm
    - kubectl

2. service の起動
    - kubernetesの起動コマンド
    ```sh
    sudo systemctl start kubernetes.service
    ```

3. 設定ファイルの作成
    1. `/etc/kubernetes/kubeadm-config.yaml` を作成
		- 以下のように設定します。
        ```yaml
        kind: ClusterConfiguration
        apiVersion: kubeadm.k8s.io/v1beta3
        kubernetesVersion: v1.23.6
        networking:
        podSubnet: 10.244.0.0/16
        ---
        kind: KubeletConfiguration
        apiVersion: kubelet.config.k8s.io/v1beta1
        cgroupDriver: cgroupfs
        ```
    2. 設定ファイルの読み込み
		- command で実行します。
        ```sh
        sudo kubeadm init --config /etc/kubernetes/kubeadm-config.yaml
        ```

4. 再起動 - 変更した設定を反映させるために以下のコマンドで再起動させます。
    ```sh
    sudo systemctl restart kubernetes.service
    ```

### kubernetes manifest の作成
起動する container の設定ファイルを作成します。設定項目は、起動する container image 名、使用するポートの設定、環境変数の設定、ストレージ、マウントの設定の５項目です。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: facedetect-on-deepstream
  name: facedetect-on-deepstream
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      run: facedetect-on-deepstream
  template:
    metadata:
      labels:
        run: facedetect-on-deepstream
    spec:
      hostname: facedetect-on-deepstream
      hostNetwork: true
      containers:
      - name: facedetect-on-deepstream
        image: latonaio/facedetect-on-deepstream:latest
        imagePullPolicy: IfNotPresent
        command: ["deepstream-app"]
        args: ["-c", "/app/src/deepstream_app_source1_facedetect.txt"]
        env:
        - name: DISPLAY
          value: "$DISPLAY"
        volumeMounts:
        - mountPath: /app/src
          name: current-dir
        - mountPath: /tmp/.X11-unix
          name: x11-dir
        - mountPath: /dev
          name: dev-dir
        securityContext:
          privileged: true
      volumes:
      - name: current-dir
        hostPath:
          path: "$PWD"
          type: Directory
      - name: x11-dir
        hostPath:
          path: /tmp/.X11-unix
          type: Directory
      - name: dev-dir
        hostPath:
          path: /dev
          type: Directory
```
### container の登録
- manifest ファイルを kubernetes に 読み込ませることで container の登録ができます。
- 準備した manifest ファイルがあるディレクトリに移動し、以下のコマンドで読み込ませることが出来ます。
	```sh
	kubectl apply -f manifest.yaml
	```
- 登録すると、`pod` と呼ばれる container 単位でサービスが起動します
- 下記のコマンドで起動中の pod 一覧を確認できます
	```sh
	kubectl get pod
	```

