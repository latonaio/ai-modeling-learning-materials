# SAPとのAPI連携統合
ラトナでは、主にエッジコンピューティング環境において、外部システムをSAPと統合することを目的とした、SAP APIランタイムを開発しています。  
これらのリソースは非常に広い範囲の機能をカバーしており、SAP S/4 HANAの機能では、Central Functions、Logistics、Inventory Management、EWM(Extended Warehouse Management)、Sales Management、Procurement Management、Production Management、Process Management、Quality Management、Plant Maintenance、Customer Service、があります。S/4 HANA以外では、C/4HANA、SuccessFactors、Concur、Fieldgrass、Sustainabilityに対応しています。
なお、全てのリソースは、クラウド環境にもそのまま適用することが可能です。  

### SAP S/4HANAにおけるラトナで開発済みのAPIランタイム
![]()

### SAP C/4HANAにおけるラトナで開発済みのAPIランタイム
![]()

### SAP SuccessFactorsにおけるラトナで開発済みのAPIランタイム
![]()

### SAP Sustainability関連におけるラトナで開発済みのAPIランタイム
![]()

### SAP API ランタイム(rmq-kube)によるSAP環境との統合について
ラトナは、SAP APIランタイムに関連して、RabbitMQ からのメッセージを受けてイベントドリブンでAPI ランタイムを実行し、アウトプットとしてRabbitMQへメッセージを出力するよう対応したAPIランタイムを開発しています。  
RabbitMQは、軽量なメッセージングアーキテクチャとして、インプット、アウトプットに対し高速で安定的なランタイム環境を提供します。

### RabbitMQを適用したSAP APIランタイムの実行フロー
RabbitMQを適用したSAP APIランタイムの実行フローは、以下の図に示されます。まず、SAP API ランタイム(rmq-kube)が、RabbiMQからのメッセージを受け取り、イベントドリブンで即座にランタイムを実行し、入力値に基づいてSAP APIをコールします。SAP APIのコールにより取得されたデータは、RabbitMQにより、SQLを更新するマイクロサービスである、sap-sql-update-kubeに渡されます。  
sap-sql-update-kubeは、RabbitMQからのメッセージを受け取り、イベントドリブンで即座にランタイムを実行します。  
そして、sap-sql-update-kubeは、渡されたデータとSQLで保持されたデータとの差分がある場合に、SQLテーブルの登録または更新を行います。

![]()

### SAP S/4HANAにおけるRabbitMQを適用したラトナで開発済みのAPIランタイム

![]()

### SAP C/4HANAにおけるRabbitMQを適用した ラトナで開発済みのAPIランタイム

![]()

### SAP関連のラトナのレポジトリ
・[sap-sandbox](https://github.com/latonaio/sap-sandbox)
・[sap-sandbox-c4hana](https://github.com/latonaio/sap-sandbox-c4hana)
・[sap-sandbox-successfactors](https://github.com/latonaio/sap-sandbox-successfactors)
・[sap-sandbox-vehicles](https://github.com/latonaio/sap-sandbox-vehicles)
・[sap-sandbox-rmq](https://github.com/latonaio/sap-sandbox-rmq)
・[sap-sandbox-rmq-c4hana](https://github.com/latonaio/sap-sandbox-rmq-c4hana)



