# Photobook API仕様 ver 1.2.5

Photobook APIの開発者向けのドキュメントです。

## エンドポイント
開発用と本番用が用意されています。詳しくはお問合せ下さい。

---
## API一覧
### 認証
* [認証 API](#認証-api)
* [ワンタイムチケット取得 API](#ワンタイムチケット取得-api)

### 商品
* [商品情報取得 API](#商品情報取得-api)
* [作品作成開始 作品キー取得 API](#作品作成開始-作品キー取得-api)

### 編集
* [編集アイテム一覧取得 API](#編集アイテム一覧取得-api)
* [編集アイテム削除 API](#編集アイテム削除-api)

### ページ
* [ページ取得 API](#ページ取得-api)

### テキスト
* [テキスト登録/更新 API](#テキスト登録更新-api)
* [テキスト取得 API](#テキスト取得-api)

### 画像
* [画像アップロード API](#画像アップロード-api) ...画像のアップロードのみでエリアには配置しない
* [アップロード済画像一覧取得 API](#アップロード済画像一覧取得-api)
* [アップロード済画像取得 API](#アップロード済画像取得-api)
* [アップロード済画像削除 API](#アップロード済画像削除-api)

### 画像配置
* [アップロード画像取得 API](#アップロード画像取得-api)
* [画像アップロード取消 API](#画像アップロード取消-api)
* [画像アップロード/更新 API](#画像アップロード更新-api) ...アップロード画像をエリアにダイレクトに配置
* [画像配置/更新 API](#画像配置更新-api) ...アップロード済の画像をエリアに配置
* [画像配置情報取得 API](#画像配置情報取得-api)
* [画像流し込み配置/更新 API](#画像流し込み配置更新-api) ...アップロード済みの画像を一括指定で一括配置

### プレビュー
* [プレビュー取得 API](#プレビュー取得-api)

### チェック
* [編集アイテム検証 API](#編集アイテム検証-api)

### カート
* [カート 開始 API](#カート-開始-api)
* [カート 作品追加 API](#カート-作品追加-api)
* [カート 作品数量変更 API](#カート-作品数量変更-api)
* [カート 作品情報取得 API](#カート-作品情報取得-api)
* [カート 作品削除 API](#カート-作品削除-api)
* [カート お届け先情報登録/更新 API](#カート-お届け先情報登録更新-api)
* [カート お届け先情報取得 API](#カート-お届け先情報取得-api)
* [カート 差出人情報登録/更新 API](#カート-差出人情報登録更新-api)
* [カート 差出人情報取得 API](#カート-差出人情報取得-api)
* [カート 請求情報取得 API](#カート-請求情報取得-api)
* [カート お支払情報登録/更新 API](#カート-お支払情報登録更新-api)
* [カート お支払情報取得 API](#カート-お支払情報取得-api)
* [カート 注文情報確認 API](#カート-注文情報確認-api)

### 注文
* [注文確定 API](#注文確定-api)
* [注文情報取得 API](#注文情報の取得-api)
* [注文キャンセル API](#注文キャンセル-api)
* [仮注文更新 API](#仮注文更新-api)

### その他
* [住所検索 API](#住所検索-api)
* [カレンダー 日付登録 API](#カレンダー-日付登録-api)
* [カレンダー 日付取得 API](#カレンダー-日付取得-api)
* [カレンダー カテゴリー取得 API](#カレンダー-カテゴリー取得-api)
* [カレンダー カラー取得 API](#カレンダー-カラー取得-api)
* [カレンダー イベント取得 API](#カレンダー-イベント取得-api)
* [カレンダー イベント登録/更新 API](#カレンダー-イベント登録更新-api)
* [カレンダー イベント削除 API](#カレンダー-イベント削除-api)
* [固定お届け先情報取得 API](#固定お届け先情報取得-api)

---
## 認証 API
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
##### ***username+passwordでの認証の場合***
API側では、作成ユーザーをusernameにて識別します。 トークンを取得する時は同一のusername、passwordを使用してください。

* grant_type [string]　: password固定
* username [string]　: client_id内で一意となる作成ユーザー識別文字列
* password(任意) [string]　: 作成ユーザーのパスワード。設定することを推奨します

##### ***リフレッシュトークンでの認証の場合***
認証APIで取得した refresh_token または ワンタイムチケット取得APIで取得した ticket を使用してください。

* grant_type [string]　: refresh_token固定
* refresh_token [string]　: 認証APIで取得したrefresh_token または ワンタイムチケット取得APIで取得したticket

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
    "expires_in": 3599,
    "refresh_token": "a7949da3309848368e27b51b6039841f",
}
```
* access_token [string]　: 弊社サーバーが発行するアクセストークン
* token_type [string]　: トークンタイプ(bearer)
* expires_in [number]　: アクセストークンの有効期間を表す秒数
* refresh_token [string]　: 弊社サーバーが発行するリフレッシュトークン

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
## ワンタイムチケット取得 API
アプリ－Webサイト間などで認証を引き継ぐ必要がある場合、ワンタイムチケットを使用し認証を引き継ぐことが可能です。
呼び出しの際は、Authorizationリクエストヘッダにアクセストークンを指定します。
ワンタイムチケットは、有効期限の短いリフレッシュトークンです。認証APIの「リフレッシュトークンでの認証」を使用してアクセストークンを取得してください。

### ***Method*** : GET
### ***Url*** : /v1/tickets
### ***Request***
なし

### ***Response***

```
{
    "ticket": "2ed2b8fcc3454757bd26facf6627dc999ed3b31d2f9848e4be7b43ac38660239",
    "expires_in": 300
}
```

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。成功|-|


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
### ***Url*** : /v1/item-specifics/{itemCode}
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
                        "minWidth": 828,
                        "minHeight": 621,
                        "cutting": [],
                        "fitType": ""
                    }
                ],
                "texts": [
                    {
                        "areaID": "TITLE-AUTHOR",
                        "maxLength": 20,
                        "maxLineLength": 20,
                        "maxLineCount": 1
                        "colors": [
                            {
                                "name": "Red",
                                "rgb": "FFFFFF"
                            },
                            {
                                "name": "Blue",
                                "rgb": "0000FF"
                            },...
                        ]
                    },
                    {
                        "areaID": "TITLE-SUBTITLE",
                        "maxLength": 40,
                        "maxLineLength": 40,
                        "maxLineCount": 1
                        "colors": [
                            {
                                "name": "Red",
                                "rgb": "FFFFFF"
                            },
                            {
                                "name": "Blue",
                                "rgb": "0000FF"
                            },...
                        ]
                    },
                    {
                        "areaID": "TITLE-TITLE",
                        "maxLength": 20,
                        "maxLineLength": 20,
                        "maxLineCount": 1
                        "colors": [
                            {
                                "name": "Red",
                                "rgb": "FFFFFF"
                            },
                            {
                                "name": "Blue",
                                "rgb": "0000FF"
                            },...
                        ]
                    }
                ]
            },
            {
                "page": 1,
                "pageType": "Body",
                "templateType": "Right",
                "images": [
                    {
                        "areaID": "PHOTO",
                        "minWidth": 828,
                        "minHeight": 621,
                        "cutting": [],
                        "fitType": ""
                    },...
                ],
                "texts": [
                    {
                        "areaID": "TEXT-DEFAULT",
                        "maxLength": 984,
                        "maxLineLength": 41,
                        "maxLineCount": 24
                        "colors": [
                            {
                                "name": "Red",
                                "rgb": "FFFFFF"
                            },
                            {
                                "name": "Blue",
                                "rgb": "0000FF"
                            },...
                        ]
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
   * page [number] : ページの番号。
   * pageType [string] : ページの種類。（Jacket：表紙, Body：本文, Front：扉, Colophon：奥付け）
   * TemplateType : テンプレートの種類。（Left：左ページ, Right：右ページ, Spread：見開き）
   * images : ページの画像エリア情報
     * areaID [string] : page内で一意となる画像エリアID。
     * minWidth [number] : 最小のwidth(px)。
     * minHeight [number] : 最小のheight(px)。
     * cutting [string] : エリアに含まれる断ち切り位置。
     * fitType [string] : エリアに対しての写真の配置方法。
   * texts : ページのテキストエリア情報
     * areaID [string] : page内で一意となる画像エリアID。
     * maxLength [number] : エリアに入力できる最大の文字数。
     * maxLineLength [number] : 一行に入力できる最大の文字数。
     * maxLineCount [number] : 最大の行数。
     * colors : 色の要素
       * name [string] : 色の名称。  
       * rgb [string] : 色RGB値。  

※　イメージのminWidth/minHeightを下回ると印刷した際に画像が荒れる可能性があります。   
※　テキストのmaxLength、およびmaxLineCountを超えないように文字を設定してください。

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。リクエスト成功|-|
|406 (Not Acceptable)|指定されたitemCodeが存在しない|notacceptable_itemcode|

---
## 作品作成開始 作品キー取得 API
### ***Method*** : POST
### ***Url*** : /v1/items/{itemCode}
### ***Request***
* itemCode : アイテムの種類を指定します。弊社にて発行したコードを指定してください。

### ***Response***
```
{
    "editKey": "3800604642207821313",
    "userId": "123",
    "expireDate": "2016/1/1"
}
```

* editKey [string] : 作成する作品を識別するためのキーです。
* userId [number] : 現在の認証ユーザーを識別するIDです。
* expireDate [datetime] : 作成する作品の編集期限です。発行日を基準にして+7日となります。例）2016/1/1に取得→2016/1/8


| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。成功|-|
|404 (Not Found)|指定されたitemCodeが存在しない|itemcode_notexist|

---
## 編集アイテム一覧取得 API
### ***Method*** : GET
### ***Url*** : /v1/edit-items/{editKey}
### ***Request***
* editKey(任意) : 作品キー取得 APIにて発行したキーを指定してください。

### ***Response***
```
{
  "editItems": [
    {
      "editKey": "4022436311773427713",
      "itemCode": "674331968573712513",
      "title": "taitoruA",
      "isOrdered": true,
      "itemPrice": 3600,
      "maxOrderCopy": 100,
      "expirationDate": "2016/03/10",
      "lastEditTime": "2016-02-25T14:14:03.463",
      "createTime": "2016-02-25T10:25:50.403"
    },...
}
```
認証ユーザーが所有する編集中のアイテムのリストを返します。
editKeyが有効期限内の編集アイテムのみ抽出します。
editKeyを指定した場合、指定したアイテムのみ返します。
* editKey [string]  : 編集中の作品を識別するためのキー
* itemCode [string]  : アイテムの種類を識別するコード
* title  [string] : 編集データにある作品のタイトル
* isOrdered [bool] : 注文の有無。trueの場合注文済、falseの場合未注文の作品であることを表します
* itemPrice [number] : アイテムの販売価格(税込)
* maxOrderCopy [number] : 作品の最大購入可能冊数
* expirationDate [datetime] : editKeyの有効期限です。有効期限を超過すると注文や編集はできません
* lastEditTime [datetime] : 編集データの最終更新日
* createTime [datetime] : editKeyの生成日

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|

---
## 編集アイテム削除 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=DELETE
### ***Url*** : /v1/edit-items/{editKey}
### ***Request***
 * editKey : 作成する作品を識別するためのキーです。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|指定されたeditKeyが見つかりません。|notacceptable_editkey|
|406 (Not Acceptable)|指定されたEditKeyが見つかりません。(注文済の作品は編集できません)|notfound_editkey|
|406 (Not Acceptable)|指定されたeditKeyは削除済みです|deleted_editkey|
|406 (Not Acceptable)|指定されたeditKeyは編集確定済みです|fixed_editkey|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notacceptable_editkey",
            "message": "指定されたeditKeyが見つかりません。",
            "moreInfo": "editKey : 1234567890"
        },...
    ]
}
```

---
## ページ取得 API
### ***Method*** : GET
### ***Url*** : /v1/{editKey}/pages/{pageNo}
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo(任意) : ページ番号を指定してください。省略時は全ページの情報を取得します。

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
                "templateId" : "BTRGSL00300"
                "images": [
                    {
                        "areaID": "JACKET",
                        "minWidth": 828,
                        "minHeight": 621,
                        "cutting": [
                            "Top",
                            "Right"
                        ],
                        "fitType": "LFit"
                    }
                ],
                "texts": [
                    {
                        "areaID": "TITLE-AUTHOR",
                        "maxLength": 20,
                        "maxLineCount": 1
                    },
                    {
                        "areaID": "TITLE-SUBTITLE",
                        "maxLength": 40,
                        "maxLineCount": 1
                    },
                    {
                        "areaID": "TITLE-TITLE",
                        "maxLength": 20,
                        "maxLineCount": 1
                    }
                ]
            },
            {
                "page": 1,
                "pageType": "Body",
                "templateType": "Right",
                "images": [
                    {
                        "areaID": "PHOTO",
                        "minWidth": 828,
                        "minHeight": 621,
                        "cutting": [
                            "Top",
                            "Right"
                        ],
                        "fitType": "LFit"
                    },...
                ],
                "texts": [
                    {
                        "areaID": "TEXT-DEFAULT",
                        "maxLength": 984,
                        "maxLineCount": 24
                        "colors": [
                            {
                                "name": "Red",
                                "rgb": "FFFFFF"
                            },
                            {
                                "name": "Blue",
                                "rgb": "0000FF"
                            },...
                        ]
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
   * page [number] : ページの番号。
   * pageType [string] : ページの種類。（Jacket：表紙, Body：本文, Front：扉, Colophon：奥付け）
   * templateType : テンプレートの種類。（Left：左ページ, Right：右ページ, Spread：見開き）
   * templateId : テンプレートを識別するためのキーです。
   * images : ページの画像エリア情報
     * areaID [string] : page内で一意となる画像エリアID。
     * minWidth [number] : 最小のwidth(px)。
     * minHeight [number] : 最小のheight(px)。
     * cutting [string] : エリアに含まれる断ち切り位置。
     * fitType [string] : エリアに対しての写真の配置方法。
   * texts : ページのテキストエリア情報
     * areaID [string] : page内で一意となる画像エリアID。
     * maxLength [number] : エリアに入力できる最大の文字数。
     * maxLineCount [number] : 最大の行数。
     * colors : 色の要素
       * name [string] : 色の名称。  
       * rgb [string] : 色RGB値。  

※　イメージのminWidth/minHeightを下回ると印刷した際に画像が荒れる可能性があります。   
※　テキストのmaxLength、およびmaxLineCountを超えないように文字を設定してください。

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。リクエスト成功|-|
|406 (Not Acceptable)|指定されたeditKeyが見つかりません。|notacceptable_editkey|
|404 (Not Found)|存在しないページが指定されました。|notfound_page|

---
## テキスト登録/更新 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=PUT
### ***Url*** : /v1/{editKey}/texts/{pageNo}
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。

```
{
    "texts": [
        {
            "areaID": "TEXT01-DEFAULT",
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
|406 (Not Acceptable)|指定されたeditKeyが存在しない|notacceptable_editkey|
|404 (Not Found)|存在しないページが指定されました。|notfound_page|
|404 (Not Found)|存在しないエリアが指定されました。|notfound_area|
|400 (Bad Request)|文字数がオーバーしました。|over_textlength|
|400 (Bad Request)|印刷できない文字が指定されました。|notprint_text|
|400 (Bad Request)|登録テキストが見つかりません。(POSTされたJSONに問題がある場合)|notfound_inputdata|
|406 (Not Acceptable)|存在しないフォントが指定されました。|notacceptable_font|
|406 (Not Acceptable)|存在しない色が指定されました。|notacceptable_color|
|406 (Not Acceptable)|存在しないサイズが指定されました。|notacceptable_size|
|406 (Not Acceptable)|タイトル以外でサイズは設定できません。|notacceptable_area|
|406 (Not Acceptable)|存在しないアライメントが指定されました|notacceptable_align|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "over_textlength",
            "message": "文字数がオーバーしました。",
            "moreInfo": "pageNo=7,areaID=TEXT01-DEFAULT,overString=○○"
        },...
    ]
}


```

---
## テキスト取得 API
### ***Method*** : GET
### ***Url*** : /v1/{editKey}/texts/{pageNo}/{areaID}
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
            "areaID": "TEXT01-DEFAULT",
            "value": "おしゃれな仕上がりと美しいレイアウトが評判のフォトブック作成サービスです。"
            "fontCode": "16"
            "fontColor": "Black"
            "fontSizeType": "Large"
            "align": "Start"
        }
    ],...
}
```
* fontCode[number] : テキストのフォントコードを取得します。
* fontColor[string] : テキストのカラーを取得します。（Black,White,Red,Green,Blue,Chrcl,Rose,Grass,Sky,Orange）
* fontSizeType[string] : テキストのサイズを取得します。（Large/Middle/Small）
* align[string] : 進行方向に対するアライメント（横書きは垂直、縦書きは水平）を取得します。(Start/Middle/End)

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。成功|-|
|404 (Not Found)|存在しないページが指定されました。|notfound_page|
|404 (Not Found)|存在しないエリアが指定されました。|notfound_area|


---
## 画像アップロード API
画像のアップロードのみを行います。  
エリアへの配置は「画像配置/更新 API」で行います。

### ***Method*** : POST
### ***Url*** : /v1/{editKey}/images
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。

### ***Request Body***
アップロードする画像ファイルを含めてください。ファイルが複数ある場合は一つ目のみが適用されます。
※最大画像サイズは20MB、対応するフォーマットはjpegのみです。

### ***Response***

```
{
    "imageId":"2-158-4-528-20160209191142-277116579"
}
```
* imageId [string] : アップロードした画像を識別するID

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|400 (Bad Request)|画像を読み込めませんでした。画像が壊れているか、画像に対応しておりません。|invalid_file|
|406 (Not Acceptable)|指定されたEditKeyが見つかりません。|notacceptable_editkey|
|406 (Not Acceptable)|指定されたEditKeyが見つかりません。(注文済の作品は編集できません))|notacceptable_editkey|
|413 (Request Entity Too Large)|ファイルサイズが大きすぎます。|toolarge_file|
|415 (Unsupported Media Type)|ファイル形式が不明です。|unsupported_file|
|415 (Unsupported Media Type)|画像ファイルを選択してください。|unsupported_file|
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
## アップロード済画像一覧取得 API
### ***Method*** : GET
### ***Url*** : /v1/{editKey}/images/
### ***QueryString***
* order : 画像のソート順を指定してください。
 + page : ページ(＆エリア)順にソート
 + upload(defalut) : アップロード順にソート
* detail : 結果の返却方法を指定してください。
 + true : 詳細モード
 + false(default) : 簡略モード
* filter : 返される画像の種類にフィルターをかけます。
 + user : ユーザーによってアップされた画像のみレスポンスに含まれます。
 + 指定なし(default) : 使用しているすべての画像をレスポンスに含めます。  
 ※固定で配置されている画像もレスポンスに含まれます。

### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。

### ***Response***
#### 詳細モード
```
{
	"images":[
    	{
        	"imageId":"2-158-4-528-20160209191142-277116579",
        	"page": "0",
        	"areaId": "PHOTO01",
        	"width": "1024"
        	"height": "768"
        	"rotate": "90"
        },
        {
        	"imageId":"2-158-4-528-20160201456546-678855441",
        	"page": "",
        	"areaId": "",
        	"width": ""
        	"height": ""
        	"rotate": ""
        },...
    ]
}

```
※未配置画像の場合は、"page""areaId""width""height""rotate"は空となる。
* imageId [string]: アップロードした画像を識別する画像ID。
* page [int]: ページ番号。
* areaId [string]: 配置されているイメージエリアのID。
* width  [int]: 画像の幅(px)。
* height [int]: 画像の高さ(px)。
* rotate [int]: 画像の回転角度。

#### 簡略モード
```
{
	"images":[
    	{
        	"imageId":"2-158-4-528-20160209191142-277116579"
        },
        {
        	"imageId":"2-158-4-528-20160201456546-678855441"
        },...
    ]
}

```
* imageId [string]: アップロードした画像を識別する画像ID。

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|指定されたeditKeyが見つかりません。|notacceptable_editkey|
|406 (Not Acceptable)|指定されたorderが不正です。|notacceptable_order|
|404 (Not Found)|ファイルが存在しません。|notfound_file|
```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notacceptable_editkey",
            "message": "指定されたeditKeyが見つかりません。",
            "moreInfo": "editKey:123456789"
        },...
    ]
}
```
---
## アップロード済画像取得 API
### ***Method*** : GET
### ***Url*** : /v1/{editKey}/images/{imageId}
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* imageId : アップロードした画像を識別する画像ID。

### ***Response***
imageIdで指定されたアップロード済画像(jpg)データを返します(バイナリ形式)。


| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (Not Found)|ファイルが存在しません。|notfound_file|
|406 (Not Acceptable)|指定されたeditKeyが見つかりません。|notacceptable_editkey|
```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notfound_file",
            "message": "ファイルが存在しません。",
            "moreInfo": "imageId:2-158-4-528-20160209191142-277116579"
        },...
    ]
}
```
---
## アップロード済画像削除 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=DELETE
### ***Url*** : /v1/{editKey}/images/{imageId}
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* imageId : アップロードした画像を識別する画像ID。

### ***Response***
imageIdで指定されたアップロード済画像(jpg)データを削除します。
使用済みの画像は指定できません。


| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (Not Found)|ファイルが存在しません。|notfound_file|
|406 (Not Acceptable)|指定されたeditKeyが見つかりません。|notacceptable_editkey|
|406 (Not Acceptable)|使用済みの画像は指定できません。|notacceptable_imageid|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notfound_file",
            "message": "ファイルが存在しません。",
            "moreInfo": "imageId:2-158-4-528-20160209191142-277116579"
        },...
    ]
}
```

---
## アップロード画像取得 API
### ***Method*** : GET
### ***Url*** : /v1/{editKey}/images/{pageNo}/{areaID}
### ***QueryString***
* h : 横幅を指定してください。
* w : 高さを指定してください。
※hかwのどちらかが指定した場合は、イメージの比率で拡縮された画像を返します。

### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。
* areaID : 取得したいイメージエリアのIDを指定してください。

### ***Response***
* image : アップロードした画像(jpg)。

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (Not Found)|存在しないページが指定されました。|notfound_page|
|404 (Not Found)|存在しないエリアが指定されました。|notfound_area|
|404 (Not Found)|ファイルが存在しません。|notfound_file|

---
## 画像アップロード取消 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=DELETE
### ***Url*** : /v1/{editKey}/images/{pageNo}/{areaID}
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。
* areaID : 取得したいイメージエリアのIDを指定してください。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (Not Found)|存在しないページが指定されました。|notfound_page|
|404 (Not Found)|存在しないエリアが指定されました。|notfound_area|
|404 (Not Found)|ファイルが存在しません。|notfound_file|

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
## 画像アップロード/更新 API
画像をアップロードし、指定したエリアに配置します。  
「画像配置/更新 API」で後から別のエリアに配置することも可能です。

### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=PUT
### ***Url*** : /v1/{editKey}/images/{pageNo}/{areaID}
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。
* areaID : 取得したいイメージエリアのIDを指定してください。

### ***Request Body***
アップロードする画像ファイルを含めてください。ファイルが複数ある場合は一つ目のみが適用されます。
※存在しない場合は、エリアに配置された画像ファイルが削除されます。  
※最大画像サイズは20MB、対応するフォーマットはjpegのみです。

### ***Response***
```
{
    "imageId":"2-158-4-528-20160209191142-277116579"
}
```
* imageId [string] : アップロードした画像を識別するID

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|400 (Bad Request)|画像を読み込めませんでした。画像が壊れているか、画像に対応しておりません。|invalid_file|
|404 (Not Found)|存在しないページが指定されました。|notfound_page|
|404 (Not Found)|存在しないエリアが指定されました。|notfound_area|
|413 (Request Entity Too Large)|ファイルサイズが大きすぎます。|toolarge_file|
|415 (Unsupported Media Type)|ファイル形式が不明です。|unsupported_file|
|416 (Requested Range Not Satisfiable)|ファイルの幅が小さすぎます。|tooshort_width|
|416 (Requested Range Not Satisfiable)|ファイルの高さが小さすぎます。|tooshort_height|
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
## 画像配置/更新 API
アップロード済の画像を指定エリアに配置します。

### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=PUT
### ***Url*** : /v1/{editKey}/images/{pageNo}/{areaID}
### ***QueryString***
* imageId : 配置する画像の画像IDを指定してください。画像IDは画像アップロードAPIの戻り値として取得します。
* rotate : 画像の回転角度。0, 90, 180, 270のみ指定可。
* x(任意) : 画像のX座標(px)
* y(任意) : 画像のY座標(px)
* w(任意) : 画像の幅(px)
* h(任意) : 画像の高さ(px)
* scale(任意) : 画像の拡大率（0.1～1.0）
※x,y,w,h,scaleは、指定した値のみが更新されます。指定が無い場合は更新されません。

### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。
* areaID : 取得したいイメージエリアのIDを指定してください。

### ***Response***
```
{
    "imageId":"2-158-4-528-20160209191142-277116579"
}
```
* imageId [string] : アップロードした画像を識別する画像ID。QueryStringで指定したものと同じIDが返ります。

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|400 (Bad Request)|画像を読み込めませんでした。画像が壊れているか、画像に対応しておりません。|invalid_file|
|404 (Not Found)|存在しないページが指定されました。|notfound_page|
|404 (Not Found)|存在しないエリアが指定されました。|notfound_area|
|406 (Not Acceptable)|指定されたEditKeyが見つかりません。|notacceptable_editkey|
|406 (Not Acceptable)|指定されたEditKeyが見つかりません。(注文済の作品は編集できません))|notacceptable_editkey|
|413 (Request Entity Too Large)|ファイルサイズが大きすぎます。|toolarge_file|
|415 (Unsupported Media Type)|ファイル形式が不明です。|unsupported_file|
|416 (Requested Range Not Satisfiable)|ファイルの幅が小さすぎます。|tooshort_width|
|416 (Requested Range Not Satisfiable)|ファイルの高さが小さすぎます。|tooshort_height|
|416 (Requested Range Not Satisfiable)|拡大率が不正です。(0.1～1.0)|tooshort_scale|
```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notfound_page",
            "message": "存在しないページが指定されました。",
            "moreInfo": "5ページ"
        },...
    ]
}
```

---
## 画像配置情報取得 API
### ***Method*** : GET
### ***Url*** : /v1/{editKey}/images/{pageNo}/{areaID}/layouts
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。
* areaID : 取得したいイメージエリアのIDを指定してください。

### ***Response***
```
{
    "imageId" : "2-158-4-528-20160209191142-277116579",
    "rotate" : 90
    "x" : 0
    "y" : 0
    "w" : 1500
    "h" : 1400
    "scale" : 0.1
}
```
* imageId [string] : アップロードした画像を識別する画像ID。
* rotate [number] : 画像の回転角度。
* x : 画像のX座標(px)
* y : 画像のY座標(px)
* w : 画像の幅(px)
* h : 画像の高さ(px)
* scale : 画像の拡大率（0.1～1.0）

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (Not Found)|存在しないページが指定されました。|notfound_page|
|404 (Not Found)|存在しないエリアが指定されました。|notfound_area|
|406 (Not Acceptable)|指定されたeditKeyが見つかりません。|notacceptable_editkey|

---
## 画像流し込み配置/更新 API
アップロードされた画像をエリアへ一括配置します。  

### ***Method*** : PUT
### ***Url*** : /v1/{editKey}/images/autoset
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。

### ***Request Body***
```
images [
    {
        "imageId":"2-158-4-528-20160209191142-277116579",
        "rotate": "90"
    },...
]
```
* imageId [string] : アップロードした画像を識別する画像ID。
* rotate [int] : 画像回転角度。


### ***Response***


| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|400 (Bad Request)|配置更新画像が見つかりません。(POSTされたJSONに問題がある場合)。|notfound_inputdata|
|400 (Bad Request)|画像を読み込めませんでした。画像が壊れているか、画像に対応しておりません。|invalid_file|
|404 (Not Found)|存在しないページが指定されました。|notfound_page|
|404 (Not Found)|存在しないエリアが指定されました。|notfound_area|
|406 (Not Acceptable)|指定されたEditKeyが見つかりません。|notacceptable_editkey|
|406 (Not Acceptable)|指定されたEditKeyが見つかりません。(注文済の作品は編集できません))|notacceptable_editkey|
|413 (Request Entity Too Large)|ファイルサイズが大きすぎます。|toolarge_file|
|415 (Unsupported Media Type)|ファイル形式が不明です。|unsupported_file|
|416 (Requested Range Not Satisfiable)|ファイルの幅が小さすぎます。|tooshort_width|
|416 (Requested Range Not Satisfiable)|ファイルの高さが小さすぎます。|tooshort_height|
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
## プレビュー取得 API
### ***Method*** : GET
### ***Url*** : /v1/{editKey}/previews/{pageNo}
### ***QueryString*** : ?areaId={areaID}&h={height}&w={width}&frame={frame}
* areaId[string]  : 商品情報取得 APIで取得したareaIDを指定してください。  
※指定したエリアを塗りつぶし、それ以外のエリアの色合いを薄くした画像を返します。
* height[int]  : 横幅を指定してください。
* width[int]  : 高さを指定してください。  
※hかwのどちらかが指定した場合は、イメージの比率で拡縮された画像を返します。
* frame[bool] : 配置されていない画像エリア、入力されていないテキストエリアすべてに色を付けます。

### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。

### ***Response***
プレビュー画像(jpg)を返します。

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (Not Found)|存在しないページが指定されました。|notfound_page|
|404 (Not Found)|存在しないエリアが指定されました。|notfound_area|

---
## 編集アイテム検証 API
### ***Method*** : GET
### ***Url*** : /v1/{editKey}/validate/
### ***Request***
 * editKey : 作成する作品を識別するためのキーです。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|指定されたeditKeyが見つかりません。|notacceptable_editkey|

```
{    
    "warningCount":5,
    "errorCount":2,
    "pages": [
        {
            "page":3,
            "warnings": [
	            {
		            "areaId": "PHOTO",
		            "message": "画像が配置されていません。"
		        },
		        {
		            "areaId": "TEXT-DEFAULT",
		            "message": "テキストが入力されていません。"
		        }
		    ],
            "errors": []
        },
        {
            "page":7,
            "warnings": [
	            {
		            "areaId": "PHOTO",
		            "message": "写真サイズが不足しています。印象時に荒く見える恐れがあります。"
		        },
		        {
		            "areaId": "TEXT-DEFAULT",
		            "message": "テキストが空です。(テキストが空白文字の場合に表示されます)"
		        }
		    ],
            "errors": [
	            {
		            "areaId": "PHOTO01",
		            "message": "使用している画像が壊れています。"
		        }
            ]
        },...
    ]
}
```

* warningCount [number] : 作成されていないエリアの総数
* errorCount [number] : 注文に影響があるエリアの総数
* pages : ページの要素
    * page [number] : ページの番号。
* warnings : Warningのリスト
    * areaId [string] : page内で一意となるエリアID。
    * message [string] : Warningの内容
	
    |  | 内容 |
    |:-----------|:------------|
    |Warning|画像が配置されていません。|
    |Warning|テキストが入力されていません。|
    |Warning|写真サイズが不足しています。印象時に荒く見える恐れがあります。|
    |Warning|テキストが空です。(テキストが空白文字の場合に表示されます)|
	
	
* errors : Errorのリスト
    * areaId [string] : page内で一意となるエリアID。
    * message [string] : Errorの内容

    |  | 内容 |
    |:-----------|:------------|
    |Error|使用している画像が壊れています。|

---
## カート 開始 API
### ***Method*** : POST
### ***Url*** : /v1/carts

### ***Response***
```
{
    "cartNo": "6302524379815465985",
    "expireDate": "2016/1/1"
}
```

* cartNo [string] : カート情報を識別するためのキーです。
* expireDate [datetime] : カートの有効期限です。発行日を基準にして+7日となります。例）2016/1/1に取得→2016/1/8


| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。成功|-|

---
## カート 作品情報取得 API
### ***Method*** : GET
### ***Url*** : /v1/carts/{cartNo}/items
### ***Request***
* cartNo : カート番号を指定します。カート開始APIで取得したコードを指定してください。

### ***Response***

```
{
    "items": [
        {
            "editKey": "4941036644930230273",
            "title": "写真日記（１）",
            "copy": 2,
            "unitPrice": 1500,
            "totalPrice": 3000,
            "maxOrderCopy": 100,
            "createTime": "2016-02-25T10:25:50.403"
        },
        {
            "editKey": "8207160303711806465",
            "title": "写真日記（２）",
            "copy": 3,
            "unitPrice": 1200,
            "totalPrice": 3600,
            "maxOrderCopy": 10,
            "createTime": "2016-02-27T10:35:50.403"
        }, ...
}
```
* items : アイテムの要素。
 * editKey [string] : 作成する作品を識別するためのキーです。
 * title [string] : 編集データにある作品のタイトルを返します。
 * copy [number] : 注文する作品の冊数。
 * unitPrice [number] : 作品の単価(決済を行わない場合は0を返します)。
 * totalPrice [number] : 作品の合計(決済を行わない場合は0を返します)。
 * maxOrderCopy [number] : 作品の最大購入可能冊数。
 * createTime [datetime] : カートアイテムの生成日時。

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。リクエスト成功|-|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notacceptable_cartno|

---
## カート 作品追加 API
### ***Method*** : POST
### ***Url*** : /v1/carts/{cartNo}/items/{editKey}
### ***Request***
* cartNo : カート番号を指定します。カート開始APIで取得したコードを指定してください。
* editKey : 作品キー取得 APIにて発行したキーを指定してください。


| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notacceptable_cartno|
|406 (Not Acceptable)|存在しないeditkeyが指定されました。|notacceptable_editkey|

---
## カート 作品数量変更 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=PUT
### ***Url*** : /v1/carts/{cartNo}/items/{editKey}
### ***Request***
* cartNo : カート番号を指定します。カート開始APIで取得したコードを指定してください。
* editKey : 作品キー取得 APIにて発行したキーを指定してください。

```
{
    "copy": 4
}
```

* copy [number] : 注文する作品の冊数。
※ 一回の注文での最大冊数は、作品ごとに決まっておりデフォルトでは最大100冊までです。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。成功|-|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notacceptable_cartno|
|406 (Not Acceptable)|存在しないeditkeyが指定されました。|notacceptable_editkey|
|406 (Not Acceptable)|冊数が指定されていません。|require_copy|
|406 (Not Acceptable)|最大購入可能冊数を超過しました。|exceeded_copylimit|

---
## カート 作品削除 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=DELETE
### ***Url*** : /v1/carts/{cartNo}/items/{editKey}
### ***Request***
* cartNo : カート番号を指定します。カート開始APIで取得したコードを指定してください。
* editKey : 作品キー取得 APIにて発行したキーを指定してください。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。成功|-|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notacceptable_cartno|
|406 (Not Acceptable)|存在しないeditkeyが指定されました。|notacceptable_editkey|

---
## カート お届け先情報登録/更新 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=PUT
### ***Url*** : /v1/carts/{cartNo}/deliveries
### ***Request***
* cartNo : カート番号を指定します。カート開始APIで取得したコードを指定してください。

```
{
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
        "companyBranch" : "千代田支店",
        "telephone" : "03-6674-2250",
        "email" : "support@mono-link.jp"
    }
}
```

* delivery : 注文の配送先要素
    * familyName [string (25)] : お届け先のお名前（姓）。
    * firstName [string (25)] : お届け先のお名前（名）。
    * zipCode [string (8)] : お届け先の郵便番号(ZZZ-ZZZZの形式、ハイフン必須)。
    * province [string (25)] : お届け先の都道府県。
    * city [string (50)] : お届け先の市区郡。
    * addressLine1 [string (50)] : お届け先の町村番地。
    * addressLine2(任意)  [string (50)]: お届け先の建物名。
    * company(任意)  [string (25)]: お届け先の会社名。
    * companyBranch(任意)  [string (25)]: お届け先の支店名。
    * telephone [string (20)] : お届け先の電話番号(ハイフンなしでもOK)。
    * email(任意)  [string (256)] : お届け先のEMailアドレス。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。成功|-|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notacceptable_cartno|
|406 (Not Acceptable)|配送先が設定されていません。|require_delivery|
|406 (Not Acceptable)|データ検証でエラーが見つかりました。(文字数制限、必須チェック、冊数チェックなど)|validate_failed|

---
## カート お届け先情報取得 API
### ***Method*** : GET
### ***Url*** : /v1/carts/{cartNo}/deliveries
### ***Request***
* cartNo : カート番号を指定します。カート開始APIで取得したコードを指定してください。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (Not Found)|お届け先情報が登録されていません。|notfound_delivery|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notacceptable_cartno|

```
{
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
            "companyBranch" : "千代田支店",
            "telephone" : "03-6674-2250",
            "email" : "support@mono-link.jp"
        }
}
```

* delivery : 注文の配送先要素
    * familyName [string] : お届け先のお名前（姓）。
    * firstName [string] : お届け先のお名前（名）。
    * zipCode [string] : お届け先の郵便番号。
    * province [string] : お届け先の都道府県。
    * city [string] : お届け先の市区郡。
    * addressLine1 [string] : お届け先の町村番地。
    * addressLine2(任意) [string] : お届け先の建物名。
    * company(任意) [string] : お届け先の会社名。
    * companyBranch(任意) [string] : お届け先の支店名。
    * telephone [string] : お届け先の電話番号。
    * email(任意) [string (256)] : お届け先のEMailアドレス。

---
## カート 差出人情報登録/更新 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=PUT
### ***Url*** : /v1/carts/{cartNo}/purchasers
### ***Request***
* cartNo : カート番号を指定します。カート開始APIで取得したコードを指定してください。

```
{
    "purchaser" : 
    {
        "familyName" : "紺天",
        "firstName" : "津太郎",
        "familyNameKana" : "コンテン",
        "firstNameKana" : "ツタロウ",
        "zipCode" : "101-0051",
        "province" : "東京都",
        "city" : "千代田区",
        "addressLine1" : "神田神保町2-4",
        "addressLine2" : "Daiwa神保町ビル5F",
        "company" : "コンテンツワークス株式会社",
        "telephone" : "03-6674-2250",
        "email" : "support@mono-link.jp"
    }
}
```

* purchaser : 注文の差出人要素
    * familyName [string (25)] : 差出人のお名前（姓）。
    * firstName [string (25)] : 差出人のお名前（名）。
    * familyNameKana(任意) [string (25)] : 差出人のフリガナ（姓）。
    * firstNameKana(任意) [string (25)] : 差出人のフリガナ（名）。
    * zipCode [string (8)] : 差出人の郵便番号(ZZZ-ZZZZの形式、ハイフン必須)。
    * province [string (25)] : 差出人の都道府県。
    * city [string (50)] : 差出人の市区郡。
    * addressLine1 [string (50)] : 差出人の町村番地。
    * addressLine2(任意)  [string (50)]: 差出人の建物名。
    * company(任意)  [string (25)]: 差出人の会社名。
    * telephone [string (20)] : 差出人の電話番号(ハイフンなしでもOK)。
    * email(任意)  [string (256)] : 差出人のEMailアドレス。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。成功|-|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notfound_cartno|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notacceptable_cartno|
|406 (Not Acceptable)|無効なcartNoが指定されました。|notacceptable_cartno|
|406 (Not Acceptable)|差出人が設定されていません。|require_purchaser|
|406 (Not Acceptable)|無効なcartNoが指定されました。|notacceptable_cartno|
|406 (Not Acceptable)|データ検証でエラーが見つかりました。(文字数制限、必須チェック、冊数チェックなど)|validate_failed|

---
## カート 差出人情報取得 API
### ***Method*** : GET
### ***Url*** : /v1/carts/{cartNo}/purchasers
### ***Request***
* cartNo : カート番号を指定します。カート開始APIで取得したコードを指定してください。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notfound_cartno|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notacceptable_cartno|
|406 (Not Acceptable)|無効なcartNoが指定されました。|notacceptable_cartno|

```
{
    "purchaser" : 
        {
            "familyName" : "紺天",
            "firstName" : "津太郎",
            "familyNameKana" : "コンテン",
            "firstNameKana" : "ツタロウ",
            "zipCode" : "101-0051",
            "province" : "東京都",
            "city" : "千代田区",
            "addressLine1" : "神田神保町2-4",
            "addressLine2" : "Daiwa神保町ビル5F",
            "company" : "コンテンツワークス株式会社",
            "telephone" : "03-6674-2250",
            "email" : "support@mono-link.jp"
        }
}
```

* purchaser : 注文の差出人要素
    * familyName [string] : 差出人のお名前（姓）。
    * firstName [string] : 差出人のお名前（名）。
    * familyNameKana(任意) [string] : 差出人のお名前（姓）。
    * firstNameKana(任意) [string] : 差出人のお名前（名）。
    * zipCode [string] : 差出人の郵便番号。
    * province [string] : 差出人の都道府県。
    * city [string] : 差出人の市区郡。
    * addressLine1 [string] : 差出人の町村番地。
    * addressLine2(任意) [string] : 差出人の建物名。
    * company(任意) [string] : 差出人の会社名。
    * telephone [string] : 差出人の電話番号。
    * email(任意) [string (256)] : 差出人のEMailアドレス。

---
## カート 請求情報取得 API
### ***Method*** : GET
### ***Url*** : /v1/carts/{cartNo}/charges
### ***Request***
* cartNo : カート番号を指定します。カート開始APIで取得したコードを指定してください。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notacceptable_cartno|

```
{
    "amount" : 4000,
    "billingItems" : [
        {
            "billingItem" : "商品代金",
            "amount" : 4500
        },
        {
            "billingItem" : "配送料",
            "amount" : 500
        }, ...
    ],
    "discount" : -1000,
    "discountItems" : [
        {
            "discountRuleCode" : "ボリュームディスカウント",
            "discountPrice" : -600
        },
        {
            "discountRuleCode" : "送料無料",
            "discountPrice" : -400
        }, ...
    ]
}
```

* amount [number] : 請求合計金額
* billingItems : 請求項目要素
    * billingItem [string] : 請求項目。
    * amount [number] : 請求項目の金額。
* discount [number] : 割引合計金額
* discountItems : 割引要素
    * discountRuleCode [string] : 割引名。
    * discountPrice [number] : 割引の金額。

---
## カート お支払情報登録/更新 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=PUT
### ***Url*** : /v1/carts/{cartNo}/payments
### ***Request***
* cartNo : カート番号を指定します。カート開始APIで取得したコードを指定してください。

```
{
    "payment" : 
    {
        "paymentType" : "creditcard",
        "cardNumber" : "1111222233334444",
        "expireMonth" : "09",
        "expireYear" : "18",
        "securityCode" : "073",
        "name" : "KONTEN TSUTARO"
    }
}
```

* payment : 注文の支払い要素
    * paymentType [string (20)] : 支払い方法。creditcard 固定。
    * cardNumber [string (16)] : クレジットカードの番号。
    * expireMonth [string (2)] : 有効期限の月(MM形式)。
    * expireYear [string (2)] : 有効期限の年(YY形式)。
    * securityCode [string] : セキュリティコード。3、4桁のみ
    * name [string] : クレジットカードの名義。  
    ※印字されているアルファベットを半角英字で指定。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|OK。成功|-|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notacceptable_cartno|
|406 (Not Acceptable)|支払い情報が設定されていません。|require_payment|
|406 (Not Acceptable)|データ検証でエラーが見つかりました。(文字数制限、必須チェックなど)|validate_failed|

---
## カート お支払情報取得 API
### ***Method*** : GET
### ***Url*** : /v1/carts/{cartNo}/payments
### ***Request***
* cartNo : カート番号を指定します。カート開始APIで取得したコードを指定してください。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notacceptable_cartno|

```
{
    "payment" : 
    {
        "paymentType" : "creditcard",
        "cardNumber" : "************4444",
        "expireMonth" : "**",
        "expireYear" : "**",
        "securityCode" : "***",
        "name" : "***"
    }
}
```
クレジットカード番号の下4桁のみご確認いただけます。

* payment : 注文の支払い要素
    * paymentType [string (20)] : 支払い方法。creditcard 固定。
    * cardNumber [string (16)] : クレジットカードの番号。
    * expireMonth [string (2)] : 有効期限の月(MM形式)。
    * expireYear [string (2)] : 有効期限の年(YY形式)。
    * securityCode [string] : セキュリティコード。3、4桁のみ
    * name [string (3)] : クレジットカードの名義。

---
## カート 注文情報確認 API
### ***Method*** : GET
### ***Url*** : /v1/carts/{cartNo}
### ***Request***
* cartNo : カート番号を指定します。カート開始APIで取得したコードを指定してください。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notacceptable_cartno|
|406 (Not Acceptable)|無効なcartNoが指定されました。|expired_cartno|
```
{
    "cart": {
        "cartNo": "6302524379815465985",
        "expireDate": "2016/5/10",
        "items": [
            {
                "editKey": "4941036644930230273",
                "title": "写真日記（１）",
                "copy": 2,
                "unitPrice" : 1500,
                "totalPrice" : 3000
            },
            {
                "editKey": "8207160303711806465",
                "title": "写真日記（２）",
                "copy": 3,
                "unitPrice" : 1200,
                "totalPrice" : 3600
            }, ...
        ],
        "amount" : 6100,
        "billingItems" : [
            {
                "billingItem" : "商品代金",
                "amount" : 6600
            },
            {
                "billingItem" : "配送料",
                "amount" : 500
            }, ...
        ],
        "payment" : 
        {
            "paymentType" : "creditcard",
            "cardNumber" : "************4444",
            "expireMonth" : "**",
            "expireYear" : "**",
            "securityCode" : "***",
            "name" : "***"
        },
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
            "companyBranch" : "千代田支店",
            "telephone" : "03-6674-2250",
            "email" : "konten@mono-link.jp"
        },
        "purchaser" : 
        {
            "familyName" : "山田",
            "firstName" : "三郎",
            "familyNameKana" : "ヤマダ",
            "firstNameKana" : "サブロウ",
            "zipCode" : "101-0051",
            "province" : "東京都",
            "city" : "千代田区",
            "addressLine1" : "神田神保町2-4",
            "addressLine2" : "APIビル45F",
            "company" : "API株式会社",
            "telephone" : "03-1234-5678",
             "email" : "support@mono-link.jp"
        },
        "discount" : -1000,
        "discountItems" : [
            {
                "discountRuleCode" : "ボリュームディスカウント",
                "discountPrice" : -600
            },
            {
                "discountRuleCode" : "送料無料",
                "discountPrice" : -400
            }, ...
        ]
    }
}
```
支払い情報は、クレジットカード番号の下4桁のみご確認いただけます。

* cart
    * cartNo [string] : カートを管理するための番号です。
    * expireDate [datetime] : カートの有効期限を返します。
    * items : 注文の商品のリスト
        * editKey [string] : 作品キー取得 APIにて発行したキーを指定してください。
        * title [string] : 作品のタイトルを返します。
        * copy [number] : 注文された冊数を返します。
        * unitPrice [number] : 作品の単価(決済を行わない場合は0を返します)。
        * totalPrice [number] : 作品の合計(決済を行わない場合は0を返します)。
    * amount [number] : 請求合計金額を返します(決済を行わない場合は0を返します)。
    * billingItems : 請求項目要素
        * billingItem [string] : 請求項目名。
        * amount [number] : 請求項目の金額。
    * payment : 注文の支払い要素
        * paymentType [string] : 支払い方法。creditcard 固定。
        * cardNumber [string] : クレジットカードの番号。
        * expireMonth [string] : 有効期限の月(MM形式)。
        * expireYear [string] : 有効期限の年(YY形式)。
        * securityCode [string] : セキュリティコード。
        * name [string] : クレジットカードの名義。
    * delivery : 注文の配送先の要素
        * familyName [string] : お届け先のお名前（姓）。
        * firstName [string] : お届け先のお名前（名）。
        * zipCode [string] : お届け先の郵便番号。
        * province [string] : お届け先の都道府県。
        * city [string] : お届け先の市区郡。
        * addressLine1 [string] : お届け先の町村番地。
        * addressLine2(任意) [string] : お届け先の建物名。
        * company(任意) [string] : お届け先の会社名。
        * companyBranch(任意) [string] : お届け先の支店名。
        * telephone [string] : お届け先の電話番号。
        * email(任意) [string] : お届け先のEMailアドレス。
    * purchaser : 注文の差出人の要素
        * familyName [string] : 差出人のお名前（姓）。
        * firstName [string] : 差出人のお名前（名）。
        * familyNameKana(任意) [string] : 差出人のフリガナ（姓）。
        * firstNameKana(任意) [string] : 差出人のフリガナ（名）。
        * zipCode [string] : お届け先の郵便番号。
        * province [string] : お届け先の都道府県。
        * city [string] : お届け先の市区郡。
        * addressLine1 [string] : お届け先の町村番地。
        * addressLine2(任意) [string] : お届け先の建物名。
        * company(任意) [string] : お届け先の会社名。
        * telephone [string] : お届け先の電話番号。
        * email(任意) [string] : お届け先のEMailアドレス。
    * discount [number] : 割引合計金額を返します。
    * discountItems : 割引要素
        * discountRuleCode [string] : 割引名。
        * discountPrice [number] : 割引の金額。

---
## 注文確定 API
### ***Method*** : POST
### ***Url*** : /v1/orders/{cartNo}
### ***Request***
* cartNo [string] (任意) : カート番号を指定します。カート開始APIで取得したコードを指定してください。  
※cartNoを指定した場合、Requestデータを設定する必要はありません。もしcartNoとRequestデータの両方を設定した場合は、Requestデータは無視されます。
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
            "companyBranch" : "千代田支店",
            "telephone" : "03-6674-2250",
            "email" : "konten@mono-link.jp"
        },
        "purchaser" : 
        {
            "familyName" : "山田",
            "firstName" : "三郎",
            "familyNameKana" : "ヤマダ",
            "firstNameKana" : "サブロウ",
            "zipCode" : "101-0051",
            "province" : "東京都",
            "city" : "千代田区",
            "addressLine1" : "神田神保町2-4",
            "addressLine2" : "APIビル45F",
            "company" : "API株式会社",
            "telephone" : "03-1234-5678",
             "email" : "support@mono-link.jp"
        ｝
}
```
* item
    * editKey [string] : 作品キー取得 APIにて発行したキーを指定してください。  
※ 注文日を基準にしてeditKeyの有効期限が+14日となります。例）2016/1/1に注文→2016/1/15
    * copy [number] : 注文する作品の冊数。  
※ 一回の注文での最大冊数は、作品ごとに決まっておりデフォルトでは最大100冊までです。
※ 注文単位での最大冊数は100冊です。

* delivery
    * familyName [string (25)] : お届け先のお名前（姓）。
    * firstName [string (25)] : お届け先のお名前（名）。
    * zipCode [string (8)] : お届け先の郵便番号(ZZZ-ZZZZの形式、ハイフン必須)。
    * province [string (25)] : お届け先の都道府県。
    * city [string (50)] : お届け先の市区郡。
    * addressLine1 [string (50)] : お届け先の町村番地。
    * addressLine2(任意)  [string (50)]: お届け先の建物名。
    * company(任意)  [string (25)]: お届け先の会社名。
    * companyBranch(任意)  [string (25)]: お届け先の支店名。
    * telephone [string (20)] : お届け先の電話番号(ハイフンなしでもOK)。
    * email(任意) [string (256)] : お届け先のEMailアドレス。
* purchaser(任意) : 注文の差出人要素
    * familyName [string (25)] : 差出人のお名前（姓）。
    * firstName [string (25)] : 差出人のお名前（名）。
    * familyNameKana(任意) [string (25)] : 差出人のフリガナ（姓）。
    * firstNameKana(任意) [string (25)] : 差出人のフリガナ（名）。
    * zipCode [string (8)] : 差出人の郵便番号(ZZZ-ZZZZの形式、ハイフン必須)。
    * province [string (25)] : 差出人の都道府県。
    * city [string (50)] : 差出人の市区郡。
    * addressLine1 [string (50)] : 差出人の町村番地。
    * addressLine2(任意)  [string (50)]: 差出人の建物名。
    * company(任意)  [string (25)]: お差出人の会社名。
    * telephone [string (20)] : 差出人の電話番号(ハイフンなしでもOK)。
    * email(任意)  [string (256)] : 差出人のEMailアドレス。

---
### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|存在しないcartNoが指定されました。|notacceptable_cartno|
|406 (Not Acceptable)|有効期限が過ぎています。|notacceptable_cartno|
|406 (Not Acceptable)|配送先が設定されていません。|require_delivery|
|406 (Not Acceptable)|商品が設定されていません。|require_item|
|406 (Not Acceptable)|指定されたeditKeyが見つかりません。|notacceptable_editkey|
|406 (Not Acceptable)|データ検証でエラーが見つかりました。(文字数制限、必須チェック、冊数チェックなど)|validate_failed|
|406 (Not Acceptable)|最大購入可能冊数を超過しました。|exceeded_copylimit|

```
【成功時の例】
{
    "orderNo": "CWIP-90123456",
    "userId" : "123"
}
```
* orderNo [string] : 注文を識別する一意の番号
* userId [number]　: 現在の認証ユーザーを識別するID

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notacceptable_cartno",
            "message": "有効期限が過ぎています。",
            "moreInfo": "CartNo:6302524379815465985"
        },
        {
            "errorCode": "exceeded_copylimit",
            "message": "最大購入可能冊数を超過しました。",
            "moreInfo": "editKey:6562980850240564225,maxOrderCopy:5"
        }
    ],...
}
```
---
## 注文情報の取得 API
### ***Method*** : GET
### ***Url*** : /v1/orders/{orderNo}
### ***Request***

* orderNo(任意) : orderNoを指定した場合は、指定した注文のみが取得できます。


### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (Not Found)|指定された注文は見つかりませんでした。|notfound_order|

```
{
    "orders": [
        {
            "orderNo" : "CWIP-90123456",
            "orderDateTime" : "2017/1/1 00:00:00",
            "status" : 2,
            "tempOrderLimitDate" : "2017/1/15 00:00:00",
            "items" : [
                {
                    editKey : "3800604642207821313",
                    title : "写真日記",
                    copy : 10,
                    unitPrice : 1600,
                    totalPrice : 16000
                },...
            ],
            "amount" : 15500,
        	"billingItems" : [
                {
                    "billingItem" : "商品代金",
                    "amount" : 16000
                },
                {
                    "billingItem" : "配送料",
                    "amount" : 500
                }, ...
        	],
            "paymentType" : "creditcard",  
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
                "companyBranch" : "千代田支店",
                "telephone" : "03-6674-2250",
                "email" : "support@mono-link.jp",
                "shipDueDate" : "2017/1/1",
                "shippingNo" : "9876543210"
            },...
            "purchaser" : 
            {
                "familyName" : "紺天",
                "firstName" : "津太郎",
                "familyNameKana" : "コンテン",
                "firstNameKana" : "ツタロウ",
                "zipCode" : "101-0051",
                "province" : "東京都",
                "city" : "千代田区",
                "addressLine1" : "神田神保町2-4",
                "addressLine2" : "Daiwa神保町ビル5F",
                "company" : "コンテンツワークス株式会社",
                "telephone" : "03-6674-2250",
                "email" : "support@mono-link.jp",
                "shipDueDate" : "2017/1/1",
                "shippingNo" : "9876543210"
            },
            "discount" : -1000
            "discountItems" : [
                {
                    "discountRuleCode" : "ボリュームディスカウント",
                    "discountPrice" : -600
                },
                {
                    "discountRuleCode" : "送料無料",
                    "discountPrice" : -400
                }, ...
            ]
        },...
    ],...
}

```

* orders
    * orderNo [string] : 注文を管理するための番号です。
    * orderDateTime [string] : 注文日時間。
    * status [number] : 注文のステータス（1:注文受付, 2:注文確定, 3:注文保留, 4:出荷済, 9:キャンセル）
    * tempOrderLimitDate [string] : 仮注文を確定注文にすることがでる期限
    * items : 注文の商品のリスト
        * editKey [string] : 作品キー取得 APIにて発行したキーを指定してください。
        * title [string] : 作品のタイトルを返します。
        * copy [number] : 注文された冊数を返します。
        * unitPrice [number] : 作品の単価(決済を行わない場合は0を返します)。
        * totalPrice [number] : 作品の合計(決済を行わない場合は0を返します)。
    * amount [number] : 請求合計金額を返します(決済を行わない場合は0を返します)。
    * billingItems : 請求項目要素（決済を行わない場合は空になります）
        * billingItem [string] : 請求項目名。
        * amount [number] : 請求項目の金額。
    * paymentType [string (20)] : 支払い方法。
    * delivery : 注文の配送先の要素
        * familyName [string] : お届け先のお名前（姓）。
        * firstName [string] : お届け先のお名前（名）。
        * zipCode [string] : お届け先の郵便番号。
        * province [string] : お届け先の都道府県。
        * city [string] : お届け先の市区郡。
        * addressLine1 [string] : お届け先の町村番地。
        * addressLine2(任意) [string] : お届け先の建物名。
        * company(任意) [string] : お届け先の会社名。
        * companyBranch(任意) [string] : お届け先の支店名。
        * telephone [string] : お届け先の電話番号。
        * email(任意) [string (256)] : お届け先のEMailアドレス。
        * shipDueDate [string] : 出荷予定日。
        * shippingNo [string] : 配送伝票番号。
    * purchaser : 注文の差出人要素
        * familyName [string] : 差出人のお名前（姓）。
        * firstName [string] : 差出人のお名前（名）。
        * familyNameKana(任意) [string] : 差出人のフリガナ（姓）。
        * firstNameKana(任意) [string] : 差出人のフリガナ（名）。
        * zipCode [string] : 差出人の郵便番号。
        * province [string] : 差出人の都道府県。
        * city [string] : 差出人の市区郡。
        * addressLine1 [string] : 差出人の町村番地。
        * addressLine2(任意)  [string]: 差出人の建物名。
        * company(任意)  [string]: お差出人の会社名。
        * telephone [string] : 差出人の電話番号。
        * email(任意)  [string] : 差出人のEMailアドレス。
    * discount [number] : 割引合計金額を返します。
	* discountItems : 割引要素
    	* discountRuleCode [string] : 割引名。
    	* discountPrice [number] : 割引の金額。

※ 注文受付から24時間以内には、注文確定となり出荷予定日が決まります。

---
## 注文キャンセル API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=DELETE
### ***Url*** : /v1/orders/{orderNo}
### ***Request***
* orderNo (任意): 注文確定 APIにて発行したOrderNoをご指定下さい。(CWIP-90123456の形式)  

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (Not Found)|指定された注文が見つかりません。|notfound_order|
|406 (Not Acceptable)|キャンセル期限が過ぎています。|not_cancel|
|406 (Not Acceptable)|既にキャンセルされています。|is_canceled|
|406 (Not Acceptable)|未確定の注文です。|invalid_status|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "not_cancel",
            "message": "キャンセル期限が過ぎています。",
            "moreInfo": "orderNo:CWIP-90123456"
        }
    ],...
}
```

---
## 仮注文更新 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=PUT
### ***Url*** : /v1/orders/{orderNo}
### ***Request***
* orderNo [string] : 注文確定 APIにて発行したOrderNoをご指定下さい。(CWIP-90123456の形式)  

---
### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (Not Found)|指定された注文が見つかりません。|notfound_order|
|406 (Not Acceptable)|更新期限が過ぎています。|not_cancel|
|406 (Not Acceptable)|既にキャンセルされています。|is_canceled|
|406 (Not Acceptable)|確定済みの注文です。|invalid_status|

```
【成功時の例】
{
    "orderNo": "CWIP-90123456",
}
```
* orderNo [string] : 注文を識別する一意の番号

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notfound_order",
            "message": "指定された注文が見つかりません。",
            "moreInfo": "OrderNo: CWIP-90123456"
        }
    ],...
}
```
---
## 住所検索 API
### ***Method*** : GET
### ***Url*** : /v1/zipcode-search/{zipCode}
### ***Request***
* zipCode [string] : 郵便番号を指定します。 (ZZZZZZZ形式 or ZZZ-ZZZZ形式）

---
### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK) | 成功 | - |
|404 (Not Found) | 指定された郵便番号が見つかりません。 | notfound_zipcode |
|406 (Not Acceptable) | 郵便番号を指定してください。 | notspecified_zipcode |
|406 (Not Acceptable) | 郵便番号のフォーマットが違います。 | incorrect_format |

```
【成功時の例】
{
    "addresses" :
    {
    	"zipCode" : "101-0051",
        "province" : "東京都",
        "city" : "千代田区",
        "addressLine1" : "神田神保町"
	},...
}
```

* addresses
	* zipCode [string] ： 郵便番号
	* province [string] ： 都道府県
	* city [string] ： 市区町村
	* addressLine1 [string] ： 町域

```
【エラーの例】
{
    "errors": [
        {
            "errorCode" : "notfound_zipcode",
            "message" : "指定された郵便番号が見つかりません。",
            "moreInfo" : "zipCode : 101-0051"
        }
    ],...
}
```

---
## カレンダー 日付登録 API
### ***Method*** : POST
### ***Url*** : /v1/{editKeys}/calendars
### ***Request***
* editKeys [string]　(必須) : 作品キー取得 APIにて発行したキーを指定してください。  

### ***QueryString***
* from : カレンダーを生成する開始年月を指定します。（yyyyMM形式 or yyyy-MM形式）  
* to : カレンダーを生成する終了年月を指定します。（yyyyMM形式 or yyyy-MM形式）  
※ from,toを指定しない場合、操作月から12ヶ月分のカレンダーを生成します。  

---
### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|指定されたeditKeyが存在しない。|notacceptable_editkey|
|406 (Not Acceptable)|存在しない日付が指定されました。|notacceptable_date|
|406 (Not Acceptable)|データ検証でエラーが見つかりました。(開始年月＞終了年月など)|validate_failed|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notacceptable_editkey",
            "message": "指定されたeditKeyが存在しない。",
            "moreInfo": "editKey:6562980850240564225"
        },
        {
            "errorCode": "notacceptable_date",
            "message": "存在しない日付が指定されました。",
            "moreInfo": "fromDate:201605,toDate:201613"
        },...
    ]
}
```

---
## カレンダー 日付取得 API
### ***Method*** : GET
### ***Url*** : /v1/{editKeys}/calendars
### ***Request***
* editKeys [string]　(必須) : 作品キー取得 APIにて発行したキーを指定してください。  

---
### ***Response***
```
{
    "fromDate": "20170101",
    "toDate": "20171231"
}
```
* fromDate [string]　: カレンダー開始年月日。（yyyyMMdd形式）  
* toDate [string]　: カテゴリー終了年月日。（yyyyMMdd形式）  
※カレンダー日付生成前の場合は、空文字が返ります。  

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|指定されたeditKeyが存在しない。|notacceptable_editkey|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notacceptable_editkey",
            "message": "指定されたeditKeyが存在しない。",
            "moreInfo": "editKey:6562980850240564225"
        },...
    ]
}
```

---
## カレンダー カテゴリー取得 API
### ***Method*** : GET
### ***Url*** : /v1/{editKeys}/calendars/categories
### ***Request***
* editKeys [string]　(必須) : 作品キー取得 APIにて発行したキーを指定してください。  

### ***Response***

```
{
    "categories": [
    	{
            "name": "anniversary.png",
            "iconUrl": "https://www.mono-link.jp/icon/anniversary.png"
        },
    	{
            "name": "birthday.png",
            "iconUrl": "https://www.mono-link.jp/icon/birthday.png"
        },...
    ]
}
```
* categories : 利用可能なイベントのカテゴリー。  
 * name [string]　: カテゴリーの名称。  
 * iconUrl [string]　: カテゴリーアイコンのURL。  

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|指定されたeditKeyが存在しない|notacceptable_editkey|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notacceptable_editkey",
            "message": "指定されたeditKeyが存在しない。",
            "moreInfo": "editKey:6562980850240564225"
        }
    ]
}
```

---
## カレンダー カラー取得 API
### ***Method*** : GET
### ***Url*** : /v1/{editKeys}/calendars/colors
### ***Request***
* editKeys [string]　(必須) : 作品キー取得 APIにて発行したキーを指定してください。  

### ***Response***

```
{
    "colors": [
    	{
            "name": "red",
            "rgb": "#FFFFFF"
        },
    	{
            "name": "blue",
            "rgb": "#0000FF"
        },...
    ]
}
```
* colors : 利用可能な色。  
 * name [string]　: 色の名称。  
 * rgb [string]　: 色RGB値。  

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|指定されたeditKeyが存在しない|notacceptable_editkey|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notacceptable_editkey",
            "message": "指定されたeditKeyが存在しない。",
            "moreInfo": "editKey:6562980850240564225"
        }
    ]
}
```

---
## カレンダー イベント取得 API
### ***Method*** : GET
### ***Url*** : /v1/{editKeys}/calendars/events/{date}
### ***Request***
* editKeys [string]　(必須) : 作品キー取得 APIにて発行したキーを指定してください。  
* date [string]　(任意) : 指定した日付のイベント情報を取得します。（yyyyMMdd形式 or yyyy-MM-dd形式）  

### ***Response***

```
{
    "events": [
        {
            "date": "20161125",
            "category": "anniversary.png",
            "color": "red"
        },
        {
            "date": "20170108",
            "category": "birthday.png",
            "color": "blue"
        },...
    ]
}
```
* date [string]　: イベントが登録された日付。  
* category [string]　: 登録されたイベントのカテゴリー。  
* color [string]　: 登録されたイベントの色。  

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|指定されたeditKeyが存在しない|notacceptable_editkey|
|406 (Not Acceptable)|存在しない日付が指定されました。|notacceptable_date|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notacceptable_editkey",
            "message": "指定されたeditKeyが存在しない。",
            "moreInfo": "editKey:6562980850240564225"
        },
        {
            "errorCode": "notacceptable_date",
            "message": "存在しない日付が指定されました。",
            "moreInfo": "date:20160230"
        },...
    ]
}
```

---
## カレンダー イベント登録/更新 API
### ***Method*** : POST
### ***Header:X-HTTP-Method-Override=PUT***
### ***Url*** : /v1/{editKeys}/calendars/events/
### ***Request***
* editKeys [string]　(必須) : 作品キー取得 APIにて発行したキーを指定してください。  

```
{
    "events": [
        {
            "date": "20161125",
            "category": "anniversary.png",
            "color": "red"
        },
        {
            "date": "20170108",
            "category": "birthday.png",
            "color": "blue"
        },...
    ]
}
```
* events : イベント  
 * date [string]　: イベントを登録/更新する日付を指定してください。（yyyyMMdd形式 or yyyy-MM-dd形式）  
 * category [string]　: 登録/更新するイベントのカテゴリーを指定してください。  
 * color [string]　: 登録/更新するイベントの色を指定してください。  

---
### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|指定されたeditKeyが存在しない|notacceptable_editkey|
|406 (Not Acceptable)|存在しない日付が指定されました。|notacceptable_date|
|406 (Not Acceptable)|存在しないカテゴリーが指定されました。|notacceptable_category|
|406 (Not Acceptable)|存在しない色が指定されました。|notacceptable_color|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notacceptable_editkey",
            "message": "指定されたeditKeyが存在しない。",
            "moreInfo": "editKey:6562980850240564225"
        },
        {
            "errorCode": "notacceptable_date",
            "message": "存在しない日付が指定されました。",
            "moreInfo": "date:20160230"
        },...
    ]
}
```

---
## カレンダー イベント削除 API
### ***Method*** : POST
### ***Header:X-HTTP-Method-Override=DELETE***
### ***Url*** : /v1/{editKeys}/calendars/events/
### ***Request***
* editKeys [string]　(必須) : 作品キー取得 APIにて発行したキーを指定してください。  

```
{
    "events": [
        {
            "date": "20161125"
        },
        {
            "date": "20170108"
        },...
    ]
}
```
* events : イベント  
 * date [string]　: イベントを削除する日付を指定してください。  

---
### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|指定されたeditKeyが存在しない|notacceptable_editkey|
|406 (Not Acceptable)|存在しない日付が指定されました。|notacceptable_date|
|406 (Not Acceptable)|指定した日付にイベントが登録されていません。|notregistered_event|

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "notacceptable_editkey",
            "message": "指定されたeditKeyが存在しない。",
            "moreInfo": "editKey:6562980850240564225"
        },
        {
            "errorCode": "notacceptable_date",
            "message": "存在しない日付が指定されました。",
            "moreInfo": "date:20160230"
        },...
    ]
}
```
---
## 固定お届け先情報取得 API
### ***Method*** : GET
### ***Url*** : /v1/connects/{connectCode}/addresses
### ***Request***
* connectCode : 接続コードを指定します。接続コードは管理画面から確認可能です。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|406 (Not Acceptable)|指定されたconnectCodeが不正です。|notacceptable_connectcode|

```
{
    "addresses" : [
        {
            "familyName" : "紺天",
            "firstName" : "津太郎",
            "zipCode" : "101-0051",
            "province" : "東京都",
            "city" : "千代田区",
            "addressLine1" : "神田神保町2-4",
            "addressLine2" : "Daiwa神保町ビル5F",
            "company" : "コンテンツワークス株式会社",
            "companyBranch" : "千代田支店",
            "telephone" : "03-6674-2250",
            "email" : "support-chiyoda@mono-link.jp"
        },
        {
            "familyName" : "紺天",
            "firstName" : "津次郎",
            "zipCode" : "210-0000",
            "province" : "神奈川県",
            "city" : "川崎市",
            "addressLine1" : "川崎区川崎1-2",
            "addressLine2" : "川崎ビル３F",
            "company" : "コンテンツワークス株式会社",
            "companyBranch" : "川崎支店",
            "telephone" : "044-1234-5678",
            "email" : "support-kawasaki@mono-link.jp"
        }
    ]
}
```

* addresses : 固定配送先要素(配列)
    * familyName [string] : お届け先のお名前（姓）。
    * firstName [string] : お届け先のお名前（名）。
    * zipCode [string] : お届け先の郵便番号。
    * province [string] : お届け先の都道府県。
    * city [string] : お届け先の市区郡。
    * addressLine1 [string] : お届け先の町村番地。
    * addressLine2(任意) [string] : お届け先の建物名。
    * company(任意) [string] : お届け先の会社名。
    * companyBranch(任意) [string] : お届け先の支店名。
    * telephone [string] : お届け先の電話番号。
    * email(任意) [string (256)] : お届け先のEMailアドレス。

---