# Photobook API仕様 ver 0.1

Photobook APIの開発者向けのドキュメントです。
現在、開発中のため調整が入る可能性があります。ご了承ください。

## エンドポイント
下記URL配下に各種APIが用意されています。

* 開発用　準備中（1月中旬）
* 本番用　準備中（1月中旬）

---
## 認証API
各種APIを利用するためには、OAuth2.0により規定された認可を行い事前にアクセストークンを取得します。  
API呼び出しの際、Authorizationヘッダーでアクセストークンを送信して認証します。

### 事前に必要なもの
認証認可を行うためには、以下の情報を事前に入手している必要があります。
* client_id　: 弊社にて発行するクライアントID
* client_secret　: 弊社にて発行するクライアントSecretキー

client_idとclient_secretの取得には、Developersへのご利用登録と利用申請が必要です。
弊社APIサービスサポート窓口へご連絡ください。

### 認可とアクセストークンの入手
クライアントプログラムは、事前に発行されたclient_id、client_secretを利用してアクセストークンを入手します。
#### ***Method*** : POST
#### ***Url*** : /oauth2/token
#### ***Request Header***
Authorization:に「"Basic " + base64encode(client_id + ":" + client_secret)」を設定します。
```
Authorization: Basic czZCaGRSa3F0MtQUUwOC0xMDM0MTVFOENGRTc=
Content-Type: application/x-www-form-urlencoded
```
#### ***x-www-form-urlencoded***
下記パラメータをリクエストボディにapplication/x-www-form-urlencoded形式で指定します。
API側では、作成ユーザーをusernameにて識別します。 トークンを取得する時は同一のusername、passwordを使用してください。

* grant_type [string]　: password固定
* username [string]　: client_id内で一意となる作成ユーザ識別文字列
* password(任意) [string]　: 作成ユーザのパスワード。設定することを推奨します

#### ***Response (status code)***

| ステータスコード | 意味|
|:-----------|:------------|
|200 (OK)|OK。リクエストが成功した。|
|400 (Bad Request)|アクセストークンの取得に失敗しました。|

#### ***Response (OK)***
```
{
    "access_token": "tc8lOyNW1Vsqx1EwXNUML6YdFJptCLblKR_sKfU2ClkikNpK3K9mK6s",
    "token_type": "bearer",
    "expires_in": 3599
}
```
* access_token [string]　: 弊社サーバーが発行するアクセストークン
* token_type [string]　: トークンタイプ(bearer)
* expires_in [int]　: アクセストークンの有効期間を表す秒数

#### ***Response (Bad Request)***
```
{
    "error": "invalid_request",
    "error_description": "リクエストに必要なパラメーターが含まれていません。"
}
```
| エラーコード | 意味|
|:-----------|:------------|
|invalid_request|リクエストに必要なパラメーターが含まれていない, サポートされないグラントタイプ以外のパラメーター値が含まれている, パラメーターが重複している, 異常値が設定されている|
|invalid_client|クライアント認証に失敗した|
|unsupported_grant_type|グラントタイプが認可サーバーによってサポートされていない|

### APIへのアクセス
入手したアクセストークンを使用して、各種APIにアクセスすることができます。  
アクセストークンの指定は共通で、APIを呼び出す際に、 Authorizationリクエストヘッダにアクセストークンを指定します。  
Authorization:に「"Bearer " + access_token」を設定します。  
※　トークンの有効期限は、1時間です。  
※　通信する経路は全て暗号化するため、httpsを利用します。
```
Authorization: Bearer tc8lOyNW1Vsqx1EwXNUML6YdFJptCLblKR_sKfU2ClkikNpK3K9mK6s
```
#### ***Response (Bad Request)***
アクセストークンが無効だった場合、APIの呼び出しは失敗し、エラー内容がレスポンスとして返却されます。
```
{
    "error": "invalid_token",
    "error_description": "不正なアクセストークンです。"
}
```
| エラーコード | 意味|
|:-----------|:------------|
|invalid_request|リクエストに必要なパラメーターが含まれていない, パラメーターが重複している, 異常値が設定されている|
|invalid_token|不正なアクセストークン, トークンの有効期限切れ|

---
## API呼び出しに関するエラーについて
### ***ステータスコード***

HTTP ステータスコードとともに結果を返します。

| ステータスコード | 意味|
|:-----------|:------------|
|200 (OK)|OK。リクエストが成功した。|
|400～|エラー|
|500～|サーバ内部エラー。処理中に例外が発生。こちらのエラーの場合はお問い合わせください。|

### ***レスポンスボディ***
エラーの場合、詳細がわかるようにレスポンスボディを返します。
```
　エラーの場合:
　{
　　"errors": [
        {
            "errorCode": "area_overflow",
            "message": "エリアでオバーフローが発生しました",
            "moreInfo": "page=7,areaID=TEXT01,orverString=です。"
        },...
    ]
　}
```
* errors : エラーのリスト
 * errorCode [string] : エラーコード
 * message [string] : エラーメッセージ
 * moreInfo [string] : エラーの場所を示す詳細な情報

---
## データモデルについて
編集データは2階層のシンプルな構造になっており、ページ(Page)とエリア(Area)です。エリアには2種類あり、TextとImageがあります。
* 編集データ
  * 1ページ目
    * イメージエリア
    * テキストエリア
  * 2ページ目
    * イメージエリア
    * テキストエリア
  * 3ページ目
    * イメージエリア
    * テキストエリア
  * ...

また、ページは4種類あり、表紙(Jacket), 本文(Body), 扉(Front), 奥付け(Colophon)があります。
### 表紙(Jacket)
表紙には、テキストエリアとしてタイトル、サブタイトル、著者名が設定できます。
![ジャケットイメージ](http://www.photoback.jp/Content/img/rough/item01.jpg "ジャケットイメージ")

### 扉(Front)
扉は開いて最初のページにあり、表紙に入力したタイトル、サブタイトル、著者名が自動で設定されます。変更はできません。
ページ番号は扉から開始するため、扉のページ番号は1になります。
![扉イメージ](http://www.photoback.jp/Content/img/rough/item06.jpg "扉イメージ")

### 本文(Body)
アップロードした写真やテキストを配置するページが本文です。扉、扉裏（白紙）があるため、ページ番号は3から始まります。
![本文イメージ](http://www.photoback.jp/Content/img/rough/item04.jpg "本文イメージ")

### 奥付け(Colophon)
奥付は最終ページで扉と同じように表紙に入力したタイトル、サブタイトル、著者名が自動で設定されます。変更はできません。
![奥付イメージ](http://www.photoback.jp/Content/img/rough/item07.jpg "奥付イメージ")



---
## 商品情報取得 API
### ***Method*** : GET
### ***Url*** : /item-specifics/{itemCode}
### ***Request***

* itemCode : アイテムの種類を指定します。弊社にて発行したコードを指定してください。

### ***Response***

```
{
    "itemSpecific": {
        "totalImageCount": 13,
        "pages": [
            {
                "page": 0,
                "pageType": "Jacket",
                "templateType": "Spread",
                "images": [
                    {
                        "areaID": "JACKET",
                        "minWidth": 82800,
                        "minHeight": 62100
                    }
                ],
                "texts": [
                    {
                        "areaID": "TITLE.AUTHOR",
                        "maxLength": 20,
                        "maxLineLength": 20,
                        "maxLineCount": 1
                    },
                    {
                        "areaID": "TITLE.SUBTITLE",
                        "maxLength": 40,
                        "maxLineLength": 40,
                        "maxLineCount": 1
                    },
                    {
                        "areaID": "TITLE.TITLE",
                        "maxLength": 20,
                        "maxLineLength": 20,
                        "maxLineCount": 1
                    }
                ]
            },
            {
                "pageNo": 1,
                "pageType": "Body",
                "templateType": "Right",
                "images": [
                    {
                        "areaID": "PHOTO",
                        "minWidth": 82800,
                        "minHeight": 62100
                    },...
                ],
                "texts": [
                    {
                        "areaID": "TEXT.DEFAULT",
                        "maxLength": 984,
                        "maxLineLength": 41,
                        "maxLineCount": 24
                    },...
                ]
            },...
        ]
    }
}
```
* itemSpecifics : アイテムの要素。
 * totalImageCount [number] : 全イメージエリアの数。
 * pages : ページの要素。
   * pageNo [number] : ページの番号。
   * pageType [string] : ページの種類。（Jacket：表紙, Body：本文, Front：扉, Colophon：奥付け）
   * TemplateType : テンプレートの種類。（Left：左ページ, Right：右ページ, Spread：見開き）
   * images : ページの画像エリア情報
     * areaID [string] : page内で一意となる画像エリアID。
     * minWidth [number] : 最小のwidth(px)。
     * minHeight [number] : 最小のheight(px)。
   * texts : ページのテキストエリア情報
     * areaID [string] : page内で一意となる画像エリアID。
     * maxLength [number] : エリアに入力できる最大の文字数。
     * maxLineLength [number] : 一行に入力できる最大の文字数。
     * maxLineCount [number] : 最大の行数。

※　イメージのminWidth/minHeightを下回ると印刷した際に画像が荒れる可能性があります。   
※　テキストのmaxLength、およびmaxLineCountを超えないように文字を設定してください。

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。リクエスト成功|-|
|406 (NOT ACCEPTABLE)|指定されたitemCodeが存在しない|notacceptable_itemcode|

---
## 作品作成開始 作品キー取得 API
### ***Method*** : POST
### ***Url*** : /items/{itemCode}
### ***Request***
* itemCode : アイテムの種類を指定します。弊社にて発行したコードを指定してください。

### ***Response***
```
{
    "editKey": "3800604642207821313",
    "expireDate": "2016/1/1"
}
```

* editKey [number] : 作成する作品を識別するためのキーです。
* expireDate [date] : 作成する作品の編集期限です。発行日を基準にして+7日となります。例）2016/1/1に取得→2016/1/8


| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。成功|-|
|406 (NOT ACCEPTABLE)|指定されたitemCodeが存在しない|itemcode_notexist|

---
## テキスト登録/更新 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=PUT
### ***Url*** : /{editKey}/texts/{pageNo}/
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。

```
{
    "texts": [
        {
            "areaID": "TEXT01.DEFAULT",
            "value": "おしゃれな仕上がりと美しいレイアウトが評判のフォトブック作成サービスです。"
        }
    ],...
}
```

* texts : テキストエリア。
 * areaID [string] : エリアID、「商品情報取得 API」にて取得したエリアIDを設定してください。
 * value [string] : テキストエリアに入力する値を設定してください。
※valueには、「商品情報取得 API」の最大文字数以上は設定しないようにしてください。  
※削除する場合は、valueに空白を設定してください。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。成功|-|
|406 (NOT ACCEPTABLE)|指定されたeditKeyが存在しない|notacceptable_editkey|
|404 (NOT FOUND)|存在しないページが指定されました。|notfound_page|
|404 (NOT FOUND)|存在しないエリアが指定されました。|notfound_area|
|400 (BAD REQUEST)|文字数がオーバーしました。|over_textlenght|
|400 (BAD REQUEST)|印刷できない文字が指定されました。|notprint_text|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "over_textlenght",
            "message": "文字数がオーバーしました。",
            "moreInfo": "pageNo=7,areaID=TEXT01,orverString=○○ です。"
        },...
    ]
}


```

---
## テキスト取得 API
### ***Method*** : GET
### ***Url*** : /{editKey}/texts/{pageNo}/{areaID}
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。
* areaID : 取得したいテキストエリアのIDを指定してください。
※省略した場合は全てのテキストエリアが取得できます。

### ***Response***

```
{
    "texts": [
        {
            "areaID": "TEXT01.DEFAULT",
            "value": "おしゃれな仕上がりと美しいレイアウトが評判のフォトブック作成サービスです。"
        }
    ],...
}
```

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。成功|-|
|404 (NOT FOUND)|存在しないページが指定されました。|notfound_page|
|404 (NOT FOUND)|存在しないエリアが指定されました。|notfound_area|


---
## 画像アップロード/更新 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=PUT
### ***Url*** : /{editKey}/images/{page}/{areaID}
### ***Request Body***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。
* areaID : 取得したいテキストエリアのIDを指定してください。

### ***Request Body***
アップロードする画像ファイルを含めてください。ファイルが複数ある場合は一つ目のみが適用されます。
※存在しない場合は、エリアに配置された画像ファイルが削除されます。  
※最大画像サイズは20MB、対応するフォーマットはjpegのみです。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (NOT FOUND)|存在しないページが指定されました。|notfound_page|
|404 (NOT FOUND)|存在しないエリアが指定されました。|notfound_area|
|413 (Request Entity Too Large)|ファイルサイズが大きすぎます。|toolarge_file|
|415 (Unsupported Media Type)|ファイル形式が不明です。|unsupported_file|
|400 (BAD REQUEST)|画像を読み込めませんでした。画像が壊れているか、画像に対応しておりません。|invalid_file|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "unsupported_file",
            "message": "ファイル形式が不明です。",
            "moreInfo": "ご利用できる画像ファイル形式はjpeg（jpg）のみです。"
        },...
    ]
}
```

---
## 画像アップロード取消 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=DELETE
### ***Url*** : /{editKey}/images/{page}/{areaID}
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。
* areaID : 取得したいテキストエリアのIDを指定してください。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (NOT FOUND)|存在しないページが指定されました。|notfound_page|
|404 (NOT FOUND)|存在しないエリアが指定されました。|notfound_area|
|404 (NOT FOUND)|ファイルが存在しません。|notfound_file|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notfound_file",
            "message": "ファイルが存在しません。",
            "moreInfo": "page=7,areaID=PHOTO01"
        },...
    ]
}
```

---
## アップロード画像取得 API
### ***Method*** : GET
### ***Url*** : /{editKey}/images/{page}/{areaID}
### ***QueryString***
* h : 横幅を指定してください。
* w : 高さを指定してください。
※hかwのどちらかが指定した場合は、イメージの比率で拡縮された画像を返します。

### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。
* areaID : 取得したいテキストエリアのIDを指定してください。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (NOT FOUND)|存在しないページが指定されました。|notfound_page|
|404 (NOT FOUND)|存在しないエリアが指定されました。|notfound_area|
|404 (NOT FOUND)|ファイルが存在しません。|notfound_file|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notfound_file",
            "message": "ファイルが存在しません。",
            "moreInfo": "page=7,areaID=PHOTO01"
        },...
    ]
}
```

---
## プレビュー取得 API
### ***Method*** : GET
### ***Url*** : /{editKey}/previews/{page}
### ***QueryString*** : ?h={height}&w={width}
* h  : 横幅を指定してください。
* w  : 高さを指定してください。
※hかwのどちらかが指定した場合は、イメージの比率で拡縮された画像を返します。

### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。

### ***Response***
プレビュー画像(jpg)を返します。

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (NOT FOUND)|存在しないページが指定されました。|notfound_page|

---
## 注文確定 API
### ***Method*** : POST
### ***Url*** : /orders/
### ***Request***

```
{
    "items" : [
        {
            "editKey": "3800604642207821313",
            "copy": 10
        },...
    ],
    "delivery" : 
    {
        "familyName" : "紺天",
        "firstName" : "津太郎",
        "zipCode" : "101-0051",
        "province" : "東京都",
        "city" : "千代田区",
        "addressLine1" : "神田神保町2-4",
        "addressLine2" : "Daiwa神保町ビル5F",
        "company" : "コンテンツワークス株式会社",
        "telephone" : "03-6674-2250"
    }
}
```
* item
    * editKey : 作品キー取得 APIにて発行したキーを指定してください。
    * copy : 注文する作品の冊数。
※ 一回の注文での最大冊数は、100冊までです。

* delivery
    * familyName [string (50)] : お届け先のお名前（姓）。
    * firstName [string (100)] : お届け先のお名前（名）。
    * zipCode [string (8)] : お届け先の郵便番号(ハイフンなしでもOK)。
    * province [string (50)] : お届け先の都道府県。
    * city [string (50)] : お届け先の市区郡。
    * addressLine1 [string (100)] : お届け先の町村番地。
    * addressLine2(任意)  [string (100)]: お届け先の建物名。
    * company(任意)  [string (100)]: お届け先の会社名。
    * telephone [string (50)] : お届け先の電話番号(ハイフンなしでもOK)。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (NOT ACCEPTABLE)|配送先が設定されていません。|require_delivery|
|406 (NOT ACCEPTABLE)|商品が設定されていません。|require_item|

```
【成功時の例】
{
    "orderNo": "OPPF-90123456",
}
```
* orderNo [string] : 注文を識別する一意の番号

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "require_delivery",
            "message": "配送先が設定されていません。",
            "moreInfo": "配送先を設定してください。"
        }
    ],...
}
```

---
## 注文情報の取得 API
### ***Method*** : GET
### ***Url*** : /orders/{orderNo}
### ***Request***

* orderNo(任意) : orderNoを指定した場合は、指定した注文のみが取得できます。


### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (NOT FOUND)|指定された注文は見つかりませんでした。|notfound_order|

```
{
    "orders": [
        {
            "orderNo" : "OPPF-90123456",
            "orderDateTime" : "2017/1/1 00:00:00",
            "status" : 2,
            "items" : [
                {
                    editKey : "3800604642207821313",
                    title : "写真日記",
                    Copy : 10,
                },...
            ],
            "delivery" : 
                {
                    "familyName" : "紺天",
                    "firstName" : "津太郎",
                    "zipCode" : "101-0051",
                    "province" : "東京都",
                    "city" : "千代田区",
                    "addressLine1" : "神田神保町2-4",
                    "addressLine2" : "Daiwa神保町ビル5F",
                    "company" : "コンテンツワークス株式会社",
                    "telephone" : "03-6674-2250",
                    "shipDueDate" : "2017/1/1"
                },...
            
        },...
    ],...
}
```


* orders
    * orderNo [string] : 注文を管理するための番号です。
    * orderDateTime [string] : 注文日時間。
    * status [number] : 注文のステータス（1:注文受付, 2:注文確定, 3:注文保留, 4:出荷済, 9:キャンセル）
    * items : 注文の商品のリスト
        * editKey [string] : 作品キー取得 APIにて発行したキーを指定してください。
        * title [string] : 編集データにある作品のタイトルを返します。
        * copy [number] : 注文された冊数を返します。
    * delivery : 注文の配送先のリスト
        * familyName [string] : お届け先のお名前（姓）。
        * firstName [string] : お届け先のお名前（名）。
        * zipCode [string] : お届け先の郵便番号。
        * province [string] : お届け先の都道府県。
        * city [string] : お届け先の市区郡。
        * addressLine1 [string] : お届け先の町村番地。
        * addressLine2(任意) [string] : お届け先の建物名。
        * company(任意) [string] : お届け先の会社名。
        * telephone [string] : お届け先の電話番号(ハイフンなしでもOK)。
        * shipDueDate [date] : 出荷予定日(注文すテータスが、2:注文確定, 4:出荷済の場合のみに返します)


※ 注文受付から24時間以内には、注文確定となり出荷予定日が決まります。
