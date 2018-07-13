# 同步工作環境到github

1. VS code 安裝 **[Settings Sync](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)**
2. 到 GitHub Developer settings 創建新的 *Personal access tokens*: https://github.com/settings/tokens
3. 權限選`gist`
4. 回到VS code按快捷鍵 `Shift + Alt + U` 把工作環境上傳,這時可以填入剛剛拿到的 *Personal access tokens*
5. 這時可以到[GitHub Gist](https://gist.github.com/)頁面檢查是否成功上傳
6. 其他地方安裝的VS code可以使用快捷鍵`Shift + Alt + D`下載剛剛上傳的工作環境
    下載時需要填入gistId: `gist.github.com/userId/gistId`

```
1. Upload Key : Shift + Alt + U
2. Download Key : Shift + Alt + D
```