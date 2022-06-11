# Notion API 検証

## インテグレーションを作成/トークン取得
https://developers.notion.com/docs/getting-started#step-1-create-an-integration

リンク先の手順に従って統合を作成し、APIを叩く際に必要なトークンを取得する
![picture 2](images/045a5016a8823a4eb10da57eeb8e008a262150be40084ff92fd379f1fea370a9.png)


## データベース内にリンクを貼り付ける
`POST https://api.notion.com/v1/pages`

![picture 4](images/fec241fd6dfd1e4c90eb7bba80f9823229fcd4b6fa223ef38bba6150ae74fccd.png)

### header
- TOKENは上記の統合時に取得したものを使用

|     | key            | value            |     |
| :-- | -------------- | ---------------- | --- |
|     | Content-Type   | application/json |     |
|     | Authorization  | Bearer TOKEN   |     |
|     | Notion-Version | 2022-02-22       |     |

### json
- `database_id`の取得方法

```json
{
 "parent": { "database_id": "{{ _.database_id }}" },
	"properties": {
		"Title": {
			"title": [
				{
					"text": {
						"content": "test"
					}
				}
			]
		},
		"URL": {
			"url": "https://developers.notion.com/docs/getting-started#step-1-create-an-integration"
		}
	}
}
```


## ページの中にページを新規作成してリンクを貼り付ける
https://developers.notion.com/docs/working-with-page-content#creating-a-page-with-content

`POST https://api.notion.com/v1/pages`

### header
- TOKENは上記の統合時に取得したものを使用する

|     | key            | value            |     |
| :-- | -------------- | ---------------- | --- |
|     | Content-Type   | application/json |     |
|     | Authorization  | Bearer TOKEN   |     |
|     | Notion-Version | 2022-02-22       |     |



### json
```json
{
  "parent": {
		"page_id": "bb2c7336-fe29-41f6-a476-66ee41484ac1",
		"type": "page_id"
	},
  "properties": {
      "title": {
  	    "title": [{ "type": "text", "text": { "content": "A note from your pals at Notion" } }]
        }
    },
    "children": [
    {
      "object": "block",
      "type": "paragraph",
      "paragraph": {
        "rich_text": [{ "type": "text", "text": { "content": "You made this page using the Notion API. Pretty cool, huh? We hope you enjoy building with us." } }]
      }
    }
  ]
}
```




## 新規ページ作成 + url貼り付け(親がデータベースの場合)
`POST https://api.notion.com/v1/pages`

### json
```json
{
	"parent": { "database_id": "{{ _.database_id }}" },
  "icon": {
  	"emoji": "🥬"
  },
	"cover": {
		"external": {
			"url": "https://upload.wikimedia.org/wikipedia/commons/6/62/Tuscankale.jpg"
		}
	},
	"properties": {
		"Name": {
			"title": [
				{
					"text": {
						"content": "Tuscan Kale"
					}
				}
			]
		},
		"Description": {
			"rich_text": [
				{
					"text": {
						"content": "A dark green leafy vegetable"
					}
				}
			]
		},
		"Food group": {
			"select": {
				"name": "Vegetable"
			}
		},
		"Price": { "number": 2.5 }
	},
	"children": [
		{
			"object": "block",
			"type": "heading_2",
			"heading_2": {
				"rich_text": [{ "type": "text", "text": { "content": "Lacinato kale" } }]
			}
		},
		{
			"object": "block",
			"type": "paragraph",
			"paragraph": {
				"rich_text": [
					{
						"type": "text",
						"text": {
							"content": "Lacinato kale is a variety of kale with a long tradition in Italian cuisine, especially that of Tuscany. It is also known as Tuscan kale, Italian kale, dinosaur kale, kale, flat back kale, palm tree kale, or black Tuscan palm.",
							"link": { "url": "https://en.wikipedia.org/wiki/Lacinato_kale" }
						}
					}
				]
			}
		}
	]
}
```
