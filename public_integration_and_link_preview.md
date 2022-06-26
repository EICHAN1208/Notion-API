# 追加調査

## NotionとのAPI連携(OAuth)

パブリックインテグレーションを作成することで、API連携できるようだった

**参考**

- 【公式】**[Authorizing public integrations](https://developers.notion.com/docs/authorization#authorizing-public-integrations)**
- **[Integration typeを「public integration」に設定してNotion API（OAuth）を試してみる](https://dev.classmethod.jp/articles/try-notion-api-with-public-integration/)**

### パブリックインテグレーション作成

[https://www.notion.so/my-integrations](https://www.notion.so/my-integrations)

でパブリックインテグレーションを作成し、下記を取得する

- OAuthクライアントID
    - 例 「88dd1e75-…-08761d3fd63d」
- OAuthクライアントシークレット
    - 例 「secret_q4Js…hoi3MxjVyVhmrpvqIg」

notionへの権限を制限できる

<img width="906" alt="image" src="https://user-images.githubusercontent.com/50512657/175796482-2c31889d-7c30-4d2f-9611-13c7ae119f57.png">


### [認可コード取得](https://developers.notion.com/docs/authorization#prompting-users-to-add-an-integration)

`GET https://api.notion.com/v1/oauth/authorize`

必要なパラメータ

<img width="809" alt="image" src="https://user-images.githubusercontent.com/50512657/175796494-0b0ab52b-061b-4427-b596-ab95c60c5cbe.png">

例

```bash
https://api.notion.com/v1/oauth/authorize
?client_id=88dd1e75-ac3a-4327-940d-08761d3fd63d
&redirect_uri=https%3A%2F%2Fwww.opening-line.co.jp%2F
&state=xyz
&response_type=code
&owner=user'
```

上記リンクをブラウザに貼り付けるとどのページの権限を委譲するか選択できる

<img width="802" alt="image" src="https://user-images.githubusercontent.com/50512657/175796502-47808b6a-fe41-4b5c-90ff-8d3e28860585.png">

<img width="733" alt="image" src="https://user-images.githubusercontent.com/50512657/175796508-5d0141bf-bbe3-4db4-a3e7-b553c6bea0bf.png">

ページがリダイレクトされ、リダイレクト先URLから認可コードを取得する

- code 「94354c87....f5becfc6」

```
https://www.opening-line.co.jp/
?code=94354c87....f5becfc6
&state=xyz
```

### [アクセストークンを取得](https://developers.notion.com/docs/authorization#exchanging-the-grant-for-an-access-token)

`GET https://api.notion.com/v1/oauth/token`

- header
    - Authorization: Basic <{client_id}:{client_secret}をbase64でエンコードした値>
    - Content-Type: application/json
- body
    - grant_type 「"authorization_code"」
    - code 「上述で取得した認可コード」
    - redirect_uri 「リダイレクト先」

(例)リクエスト

```bash
curl --request POST \
  --url https://api.notion.com/v1/oauth/token \
  --header 'Authorization: Basic <{client_id}:{client_secret}をbase64でエンコードした値>' \
  --header 'Content-Type: application/json' \
  --data '{
	"grant_type": "authorization_code",
	"code": <上記で取得した認可コード>,
	"redirect_uri": "リダイレクト先"
}'
```

レスポンス

```json
{
	"access_token": "secret_PONYIm...UVlzJvjvlOimVrJL",
	"token_type": "bearer",
	"bot_id": "b16ca27b-9c26-46ce-9b6f-571c7833e44a",
	"workspace_name": "Murakami",
	"workspace_icon": "https://lh3.googleusercontent.com/a/AATXAJwhMPtZw_IONGC8k6N0vXHUf7NjySygG5bsMt0=s100",
	"workspace_id": "6023ddb3-d594-4ace-bf27-e4f6ae448bcd",
	"owner": {
		"type": "user",
		"user": {
			"object": "user",
			"id": "f02f0573-fb94-4eef-a153-9e806f3f3ba4"
		}
	}
}
```

アクセストークンを使用してリクエストできるページ・権限(読み取り・書き込み)を絞り込むことができた

## リンクをプレビューして貼り付ける

**結論**

- [プレビューして貼り付けるのは、できなさそう](https://developers.notion.com/reference/block#link-preview-blocks)
- ブックマークとして貼り付けるのは可能(後述)

<img width="870" alt="image" src="https://user-images.githubusercontent.com/50512657/174439181-e03ae252-2ac9-4a10-a49e-f7aefb05d694.png">


### ブックマークとプレビューの違い

ブックマーク

<img width="788" alt="image" src="https://user-images.githubusercontent.com/50512657/174439203-41bd74b9-7336-4cc3-8b40-80df6511cf93.png">


プレビュー

<img width="770" alt="image" src="https://user-images.githubusercontent.com/50512657/174439216-57400379-ce06-407e-a3b9-41af0e5a8f7d.png">


## ブックマークとして貼り付ける

### **貼り付けのイメージ**

ブックマーク + コメントの追加

<img width="1553" alt="image" src="https://user-images.githubusercontent.com/50512657/175754892-838d8c96-4814-432f-88bf-8a4691bbf238.png">


### [Append block children](https://developers.notion.com/reference/patch-block-children)

`POSR https://api.notion.com/v1/blocks/{block_id}/children`

block_id

- page_idと同じなので、ページurlの一部を抜粋して使用する

**JSON**

```json
{
	"children": [
		{
			"object": "block",
			"type": "bookmark",
			"bookmark": {
        "caption": [
            {
                "type": "text",
                "text": {
                    "content": "add comment",
                    "link": null
                },
                "annotations": {
                    "bold": false,
                    "italic": false,
                    "strikethrough": false,
                    "underline": false,
                    "code": false,
                    "color": "default"
                },
                "plain_text": "My caption",
                "href": null
            }
        ],
        "url": "http://www.notion.so"
			}
		}
	]
}
```
