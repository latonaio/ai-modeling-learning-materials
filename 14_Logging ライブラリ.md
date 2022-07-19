# Logging ライブラリ
ラトナでは、マイクロサービス の ログ を出力する際に、ログの json フォーマットを統一するためのライブラリを開発しています。  
ライブラリが対応しているランタイムは、Golang、Python、Node.jsです。  

### 利用方法
ラトナのGithub上に公開されたレポジトリを用います。  
・[Golang ランタイム用](https://github.com/latonaio/golang-logging-library)
・[Python ランタイム用](https://github.com/latonaio/python-logging-library)
・[Node.jsランタイム用](https://github.com/latonaio/nodejs-logging-library)

##### レポジトリのインストール(例：Golang)
go getでインストールします。
```
go get github.com/latonaio/golang-logging-library/logger@v1.0.4 
```
または、各マイクロサービスのgo.modに以下のように定義してから、go mod downloadでインストールしてください。
```
module MODULE-NAME

go 1.18

require (
	github.com/latonaio/golang-logging-library v1.0.4
)

```
```
go mod download   #全てインストールする場合
go mod download github.com/latonaio/golang-logging-library@v1.0.4   #一部のみインストールする場合
```
各マイクロサービスのソース内に以下を配置します
```
import "github.com/latonaio/golang-logging-library/logger"
```
インスタンスの作成を実行します
```
l := logger.NewLogger()
```

### 出力の形式
出力の形式は、以下のものがあります。
・log.Fatal(msg)
・log.Error(msg)
・log.Warn (msg)
・log.Info (msg)
・log.Debug(msg)

### ログの出力例
msg: interface型、文字列で渡した場合と、JSONにマッピングできるmapや構造体の形式で渡した場合で、Loggingライブラリの挙動が異なります。

```
// 引数に文字列を渡した場合
logging.Debug("This is Test")
{"cursor":"/Users/xxx/test/test.go#L35","level":"DEBUG","message":"This is Test","time":"2021-11-05T18:33:49.495918+09:00"}

// 引数を文字列に渡した場合、フォーマット指定することも可能です
var A = "test"
var B = 111
logging.Debug("%v %v", A, B)
{"cursor":"/Users/xxx/test/test.go#L36","level":"DEBUG","message":"test 111","time":"2021-11-05T18:33:49.496388+09:00"}

// JSONにマッピング可能なマップを渡した場合
var testJson1 = map[string]interface{}{
    "message": "debug",
    "params": []string{
        "nested", "world",
    },
}
logging.Debug(testJson1)
{"cursor":"/Users/xxx/test/test.go#L39","level":"DEBUG","message":"debug","params":["nested","world"],"time":"2021-11-05T18:33:49.496445+09:00"}

// JSONにマッピング可能な構造体を渡した場合
type testStruct struct {
	This string `json:"message"`
	Is   string `json:"is"`
	Test int    `json:"test"`
}

var testJson2 = testStruct{
    "hello", "world", 100,
}
logging.Debug(testJson2)
{"cursor":"/Users/xxx/test/test.go#L40","is":"world","level":"DEBUG","message":"hello","test":100,"time":"2021-11-05T18:33:49.496519+09:00"}
```

### SAP向けGolang Logging ライブラリ
ラトナでは、Golang Logging ライブラリをベースとして、SAPの OPEN API(ODATA) における日付/時間項目のJSONフォーマットを一般的な日付時間のJSONフォーマットに変換する機能を追加した、SAPリソース向けGolang Logging ライブラリを開発しています。

### 利用方法
ラトナのGithub上に公開されたレポジトリを用います。
具体的な手順は、Logging ライブラリと同様です。
・[golang-logging-library-for-sap](https://github.com/latonaio/golang-logging-library-for-sap)

### 日付の変換
Golang Logging ライブラリ が扱うJSONフォーマットに、SAP の日付/時間書式である項目が含まれる場合、当該項目値の変換を行います。  
なお、SAPの日付/時間項目であるかどうかの判断基準は、項目値が <"/Date("で始まり>、かつ、<")/"で終わり>、かつ、<これらの間の値が全て数字である>、の条件となっています。  

### 変換前のフォーマット例
下記の "CreationDate"、"LastChangeDate"が変換前の項目と項目値の例です。  
```
{
	"cursor": "/Users/latona2/bitbucket/sap-api-integrations-product-master-reads/SAP_API_Caller/caller.go#L98",
	"function": "sap-api-integrations-product-master-reads/SAP_API_Caller.(*SAPAPICaller).General",
	"level": "INFO",
	"message": [
		{
			"Product": "A001",
			"IndustrySector": "M",
			"ProductType": "SERV",
			"BaseUnit": "AU",
			"ValidityStartDate": "",
			"ProductGroup": "A001",
			"Division": "00",
			"GrossWeight": "0.000",
			"WeightUnit": "KG",
			"SizeOrDimensionText": "",
			"ProductStandardID": "",
			"CreationDate": "/Date(1466380800000)/",
			"LastChangeDate": "/Date(1491868800000)/",
			"IsMarkedForDeletion": false,
			"NetWeight": "0.000",
			"ChangeNumber": "",
			"to_Description": "https://sandbox.api.sap.com/s4hanacloud/sap/opu/odata/sap/API_PRODUCT_SRV/A_Product('A001')/to_Description"
		}
	],
	"time": "2022-01-08T10:38:23.402329+09:00"
}
```
### 変換後のフォーマット例
下記の "CreationDate"、"LastChangeDate"が変換後の項目と項目値の例です。
```
{
	"cursor": "/Users/latona2/bitbucket/sap-api-integrations-product-master-reads/SAP_API_Caller/caller.go#L98",
	"function": "sap-api-integrations-product-master-reads/SAP_API_Caller.(*SAPAPICaller).General",
	"level": "INFO",
	"message": [
		{
			"Product": "A001",
			"IndustrySector": "M",
			"ProductType": "SERV",
			"BaseUnit": "AU",
			"ValidityStartDate": "",
			"ProductGroup": "A001",
			"Division": "00",
			"GrossWeight": "0.000",
			"WeightUnit": "KG",
			"SizeOrDimensionText": "",
			"ProductStandardID": "",
			"CreationDate": "2016-06-20T09:00:00+09:00",
			"LastChangeDate": "2017-04-11T09:00:00+09:00",
			"IsMarkedForDeletion": false,
			"NetWeight": "0.000",
			"ChangeNumber": "",
			"to_Description": "https://sandbox.api.sap.com/s4hanacloud/sap/opu/odata/sap/API_PRODUCT_SRV/A_Product('A001')/to_Description"
		}
	],
	"time": "2022-01-08T10:38:23.402329+09:00"
}
```


