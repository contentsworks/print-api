# Photobook API仕様 ver 1.2.2

Photobook APIの開発者向けのドキュメントです。

## エンドポイント
開発用と本番用が用意されています。詳しくはお問合せ下さい。

---
## API一覧
### 認証
* [認証 API](#認証-api)
* [ワンタイムチケット取得 API](#ワンタイムチケット取得-api)

### 編集
* [商品情報取得 API](#商品情報取得-api)
* [作品作成開始 作品キー取得 API](#作品作成開始-作品キー取得-api)
* [テキスト登録/更新 API](#テキスト登録/更新-api)
* [テキスト取得 API](#テキスト取得-api)
* [画像アップロード API](#画像アップロード-api) ...画像のアップロードのみでエリアには配置しない
* [画像アップロード/更新 API](#画像アップロード/更新-api) ...アップロード画像をエリアにダイレクトに配置
* [画像配置/更新 API](#画像配置/更新-api) ...アップロード済の画像をエリアに配置
* [画像流し込み配置/更新 API](#画像流し込み配置/更新-api) ...アップロード済みの画像を一括指定で一括配置
* [画像アップロード取消 API](#画像アップロード取消-api)
* [アップロード画像取得 API](#アップロード画像取得-api)
* [アップロード済画像一覧取得 API](#アップロード済画像一覧取得-api)
* [アップロード済画像取得 API](#アップロード済画像取得-api)
* [画像配置情報取得 API](#画像配置情報取得-api)
* [編集アイテム一覧取得 API](#編集アイテム一覧取得-api)
* [アップロード済画像削除 API](#アップロード済画像削除-api)
* [プレビュー取得 API](#プレビュー取得-api)
* [編集アイテム検証 API](#編集アイテム検証-api)

### カート
* [カート 開始 API](#カート-開始-api)
* [カート 作品追加 API](#カート-作品追加-api)
* [カート 作品数量変更 API](#カート-作品数量変更-api)
* [カート 作品情報取得 API](#カート-作品情報取得-api)
* [カート 作品削除 API](#カート-作品削除-api)
* [カート お届け先情報登録/更新 API](#カート-お届け先情報登録/更新-api)
* [カート お届け先情報取得 API](#カート-お届け先情報取得-api)
* [カート 請求情報取得 API](#カート-請求情報取得-api)
* [カート お支払情報登録/更新 API](#カート-お支払情報登録/更新-api)
* [カート お支払情報取得 API](#カート-お支払情報取得-api)
* [カート 注文情報確認 API](#カート-注文情報確認-api)

### 注文
* [注文確定 API](#注文確定-api)
* [注文情報取得 API](#注文情報の取得-api)
* [注文キャンセル API](#注文キャンセル-api)
* [仮注文更新 API](#仮注文更新-api)

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
                    }
                ],
                "texts": [
                    {
                        "areaID": "TITLE-AUTHOR",
                        "maxLength": 20,
                        "maxLineLength": 20,
                        "maxLineCount": 1
                    },
                    {
                        "areaID": "TITLE-SUBTITLE",
                        "maxLength": 40,
                        "maxLineLength": 40,
                        "maxLineCount": 1
                    },
                    {
                        "areaID": "TITLE-TITLE",
                        "maxLength": 20,
                        "maxLineLength": 20,
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
                        "minHeight": 621
                    },...
                ],
                "texts": [
                    {
                        "areaID": "TEXT-DEFAULT",
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
   * page [number] : ページの番号。
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

```
【エラーの例】
{
    "errors": [
        {
            "errorCode": "over_textlength",
            "message": "文字数がオーバーしました。",
            "moreInfo": "pageNo=7,areaID=TEXT01,orverString=○○ です。"
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
        }
    ],...
}
```

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
## 画像アップロード/更新 API
画像をアップロードし、指定したエリアに配置します。  
「画像配置/更新 API」で後から別のエリアに配置することも可能です。

### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=PUT
### ***Url*** : /v1/{editKey}/images/{pageNo}/{areaID}
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。
* areaID : 取得したいテキストエリアのIDを指定してください。

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

### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。
* areaID : 取得したいテキストエリアのIDを指定してください。

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
## 画像流し込み配置/更新 API
画像のエリアへの配置を一括で行います。  

### ***Method*** : PUT
### ***Url*** : /v1/{editKey}/images/autoset
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。

### ***Request Body***
配置する画像ファイルを指定してください。

### ***Response***

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
## 画像アップロード取消 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=DELETE
### ***Url*** : /v1/{editKey}/images/{pageNo}/{areaID}
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。
* areaID : 取得したいテキストエリアのIDを指定してください。

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
* areaID : 取得したいテキストエリアのIDを指定してください。

### ***Response***
* image : アップロードした画像(jpg)。

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (Not Found)|存在しないページが指定されました。|notfound_page|
|404 (Not Found)|存在しないエリアが指定されました。|notfound_area|
|404 (Not Found)|ファイルが存在しません。|notfound_file|

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
        	"rotate": "90"
        },
        {
        	"imageId":"2-158-4-528-20160201456546-678855441",
        	"page": "",
        	"areaId": "",
        	"rotate": ""
        },...
    ]
}

```
※未配置画像の場合は、"page""areaId""rotate"は空となる。
* imageId [string]: アップロードした画像を識別する画像ID。
* page [int]: ページ番号。
* areaId [string]: 配置されているテキストエリアのID。
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
## 画像配置情報取得 API
### ***Method*** : GET
### ***Url*** : /v1/{editKey}/images/{pageNo}/{areaID}/layouts
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* pageNo : ページ番号を指定してください。
* areaID : 取得したいテキストエリアのIDを指定してください。

### ***Response***
```
{
	"imageId" : "2-158-4-528-20160209191142-277116579",
    "rotate" : 90
}
```
* imageId [string] : アップロードした画像を識別する画像ID。
* rotate [number] : 画像の回転角度。

| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (Not Found)|存在しないページが指定されました。|notfound_page|
|404 (Not Found)|存在しないエリアが指定されました。|notfound_area|
|406 (Not Acceptable)|指定されたeditKeyが見つかりません。|notacceptable_editkey|

---

## アップロード済画像削除 API
### ***Method*** : POST
### ***Header*** : X-HTTP-Method-Override=DELETE
### ***Url*** : /v1/{editKey}/images/{imageId}/
### ***Request***
* editKey : 作品キー取得 APIにて発行したキーを指定してください。
* imageId : 配置する画像の画像IDを指定してください。画像IDは画像アップロードAPIの戻り値として取得します。

### ***Response***
| ステータスコード | 意味|エラーコード|
|:-----------|:------------|:------------|
|200 (OK)|成功|-|
|404 (Not Found)|ファイルが存在しません。|notfound_file|
|406 (Not Acceptable)|指定されたimageIdが見つかりません。|notacceptable_imageId|
|406 (Not Acceptable)|指定されたeditKeyが見つかりません。|notacceptable_editkey|

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
## 編集アイテム一覧取得 API
### ***Method*** : GET
### ***Url*** : /v1/edit-items/
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
## プレビュー取得 API
### ***Method*** : GET
### ***Url*** : /v1/{editKey}/previews/{pageNo}
### ***QueryString*** : ?areaId={areaID}&h={height}&w={width}
* areaId  : 商品情報取得 APIで取得したareaIDを指定してください。  
※指定したエリアを塗りつぶし、それ以外のエリアの色合いを薄くした画像を返します。
* height  : 横幅を指定してください。
* width  : 高さを指定してください。
※hかwのどちらかが指定した場合は、イメージの比率で拡縮された画像を返します。

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
		            "message": "画像が配置されていません。"
		        },
		        {
		            "areaId": "TEXT-DEFAULT",
		            "message": "テキストが入力されていません。"
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
    * errors : Errorのリスト
        * areaId [string] : page内で一意となるエリアID。
        * message [string] : Errorの内容

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
            "maxOrderCopy": 100
        },
        {
            "editKey": "8207160303711806465",
            "title": "写真日記（２）",
            "copy": 3,
            "unitPrice": 1200,
            "totalPrice": 3600,
            "maxOrderCopy": 10
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
        "telephone" : "03-6674-2250"
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
    * telephone [string (20)] : お届け先の電話番号(ハイフンなしでもOK)。

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
            "telephone" : "03-6674-2250"
        }
}
```

* delivery : 注文の配送先要素
    * familyName [string] : お届け先のお名前（姓）。
    * firstName [string] : お届け先のお名前（名）。
    * zipCode [string] : お届け先の郵便番号。
    * province [string] : お届け先の都道府県。
    * city(任意) [string] : お届け先の市区郡。
    * addressLine1 [string] : お届け先の町村番地。
    * addressLine2(任意) [string] : お届け先の建物名。
    * company(任意) [string] : お届け先の会社名。
    * telephone [string] : お届け先の電話番号(ハイフンなしでもOK)。

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
    "amount" : 5000,
    "billingItems" : [
        {
            "billingItem" : "商品代金",
            "amount" : 4500
        },
        {
            "billingItem" : "配送料",
            "amount" : 500
        }, ...
    ]
}
```

* amount [number] : 請求合計金額
* billingItems : 請求項目要素
    * billingItem [string] : 請求項目。
    * amount [number] : 請求項目の金額。

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
        "amount" : 7100,
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
            "telephone" : "03-6674-2250"
        }
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
        * paymentType [string (20)] : 支払い方法。creditcard 固定。
        * cardNumber [string (16)] : クレジットカードの番号。
        * expireMonth [string (2)] : 有効期限の月(MM形式)。
        * expireYear [string (2)] : 有効期限の年(YY形式)。
        * securityCode [string] : セキュリティコード。
        * name [string (3)] : クレジットカードの名義。
    * delivery : 注文の配送先の要素
        * familyName [string] : お届け先のお名前（姓）。
        * firstName [string] : お届け先のお名前（名）。
        * zipCode [string] : お届け先の郵便番号。
        * province [string] : お届け先の都道府県。
        * city [string] : お届け先の市区郡。
        * addressLine1 [string] : お届け先の町村番地。
        * addressLine2(任意) [string] : お届け先の建物名。
        * company(任意) [string] : お届け先の会社名。
        * telephone [string] : お届け先の電話番号(ハイフンなしでもOK)。

---
## 注文確定 API
### ***Method*** : POST
### ***Url*** : /v1/orders/{cartNo}
### ***Request***
* cartNo [string] (任意) : カート番号を指定します。カート開始APIで取得したコードを指定してください。  
※cartNoを指定した場合、Requestは必要ありません。
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
    * telephone [string (20)] : お届け先の電話番号(ハイフンなしでもOK)。

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
            "amount" : 16500,
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
                "telephone" : "03-6674-2250",
                "shipDueDate" : "2017/1/1",
                "shippingNo" : "9876543210"
            },...
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
        * telephone [string] : お届け先の電話番号(ハイフンなしでもOK)。
        * shipDueDate [string] : 出荷予定日。
        * shippingNo [string] : 配送伝票番号。

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
            "moreInfo": "OrderNo": "CWIP-90123456"
        }
    ],...
}
```
---
