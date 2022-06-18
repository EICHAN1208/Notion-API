# 追加調査

## NotionとのAPI連携

パブリックインテグレーションを作成することで、API連携できるようだった

**参考**

- 【公式】**[Authorizing public integrations](https://developers.notion.com/docs/authorization#authorizing-public-integrations)**
- **[Integration typeを「public integration」に設定してNotion API（OAuth）を試してみる](https://dev.classmethod.jp/articles/try-notion-api-with-public-integration/)**

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
				"url": "https://www.yahoo.co.jp/"
			}
		}
	]
}
```
