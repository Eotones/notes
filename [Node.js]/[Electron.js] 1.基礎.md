###### tags: `js`, `node.js`

# Electron.js 筆記

Electron.js 官網:
https://electronjs.org/

JS ES6入門(中文教學):
http://es6.ruanyifeng.com/

## 基礎

Electron.js 是基於 Node.js 的跨平台桌面應用程式框架
以傳統網頁的形式來說 **主進程 Main Process** 就類似伺服端的PHP
**渲染進程 Renderer Process** 就類似運行在用戶端瀏覽器的Javascript
> 注意以上只是比較好懂的解釋方法,和實際運作方式不太一樣
> 因為這是桌面應用程式
> **主進程 Main Process** 是使用node.js直接運行在用戶端的桌面程式
> **渲染進程 Renderer Process** 是運行在Electron.js內嵌的Chromium瀏覽器裡的js
> 真正的伺服端不這個框架的架構中(可以自己另外用node.js去寫伺服端程式)



* **主進程 ( Main Process )**
    * [app](https://electronjs.org/docs/api/app) - 控制app生命週期
    * [autoUpdater](https://electronjs.org/docs/api/auto-updater) - 使應用程式自動更新
    * [BrowserWindow](https://electronjs.org/docs/api/browser-window) - 創建和控制瀏覽器視窗
    * [contentTracing](https://electronjs.org/docs/api/content-tracing) - 從Chromium的內容模塊收集跟踪數據
    * [dialog](https://electronjs.org/docs/api/dialog) - 顯示用於打開和保存文件、警報等的本機系統對話框。
    * [ipcMain](https://electronjs.org/docs/api/ipc-main) - 從主進程到渲染進程的異步通信。
    * [globalShortcut](https://electronjs.org/docs/api/global-shortcut) - 當應用程序沒有鍵盤焦點時監聽全局鍵盤事件。
    * [Menu](https://electronjs.org/docs/api/menu)
    * [MenuItem](https://electronjs.org/docs/api/menu-item)
    * [powerMonitor](https://electronjs.org/docs/api/power-monitor) - 監控電源狀態的改變
    * [powerSaveBlocker](https://electronjs.org/docs/api/power-save-blocker) - 阻止系統進入低功耗(休眠)模式
    * [protocol](https://electronjs.org/docs/api/protocol) - 註冊自定義協議並攔截基於現有協議的請求。
    * [session](https://electronjs.org/docs/api/session) - 管理瀏覽器會話、cookie、緩存、代理設置等。
    * [systemPreferrences](https://electronjs.org/docs/api/system-preferences)
    * [Tray](https://electronjs.org/docs/api/tray) - 添加系統圖示和選單到系統通知區
* **共通進程 ( Both )**
    * [clipboard](https://electronjs.org/docs/api/clipboard) - 系統剪貼簿
    * [crashReport](https://electronjs.org/docs/api/crash-reporter) - 系統崩潰日誌回報
    * [nativeImage](https://electronjs.org/docs/api/native-image) - 使用 PNG 或 JPG 文件創建托盤、dock和應用程序圖標。
    * [shell](https://electronjs.org/docs/api/shell) - 使用默認應用程序管理文件和 url。
    * [screen](https://electronjs.org/docs/api/screen) - 檢索有關屏幕大小、顯示器、光標位置等的信息。
* **渲染進程 ( Renderer Process )**
    * [desktopCapturer](https://electronjs.org/docs/api/desktop-capturer) - 從整個桌面捕獲音頻和視頻
    * [ipcRenderer](https://electronjs.org/docs/api/ipc-renderer) - 從渲染器進程到主進程的異步通信。
    * [remote](https://electronjs.org/docs/api/remote) - 在渲染進程中使用主進程模塊。
    * [webFrame](https://electronjs.org/docs/api/web-frame) - 自定義渲染當前網頁
    * DOM/Browser APIs
    * <[webview](https://electronjs.org/docs/api/webview-tag)>

