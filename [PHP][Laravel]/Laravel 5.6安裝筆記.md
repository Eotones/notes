# Laravel 5.6安裝筆記

**Laravel官網:** https://laravel.com/

Laravel中文文檔: https://laravel-china.org/docs/laravel/5.6
Laravel中文教學: https://github.com/johnlui/Learn-Laravel-5

---

這篇是測試將**Laravel 5.6**裝在**WAMP**上的筆記,並且不是裝在網頁根目錄,不是官方裝法

* WAMP路徑:
```
wamp/www/你的資料夾/
```
* 網頁路徑:
```
http://localhost/你的資料夾/
```

## 環境

* WIN7 64bit
* WAMP 3.1.3 64bit
    * Apache 2.4.33
    * PHP 7.2.4
    * MySQL 5.7.21

WAMP是抓官網最新版 http://www.wampserver.com/en/
然後**Laravel 5.6**要求**PHP**要**7.1.3以上**版本
裝完WAMP後請記得將WAMP的**PHP**切換到**7.2**或7.1.3以上版本(在右下系統圖示點左鍵可以直接切換PHP版本)

## 安裝

1. 先安裝[composer](https://getcomposer.org/),並確定`composer`指令能執行
2. 直接建立laravel project (先cd切換到你要安裝的資料夾)
(還有這安裝下載會有點久,要等他跑完)
```shell=
composer create-project laravel/laravel --prefer-dist
```
3. 此時目錄結構:
```
wamp/www/你的資料夾/
wamp/www/你的資料夾/laravel/(底下是一堆laravel安裝的東西)
```
4. 先開啟以下網址檢查**laravel**是否有正確運行再繼續執行以下步驟
```
http://localhost/你的資料夾/laravel/public/
```
5. 將`laravel/`下的`server.php`複製到`你的資料夾/`下並改名成`index.php`
```
wamp/www/你的資料夾/
wamp/www/你的資料夾/laravel/(底下是一堆laravel安裝的東西)
wamp/www/你的資料夾/index.php
```
6. 將`wamp/www/你的資料夾/index.php`中的`require_once`重新指向到正確位置
```php
require_once __DIR__.'/laravel/public/index.php';
```
7. 將`laravel/public/`下的`.htaccess`搬到`你的資料夾/`
```
wamp/www/你的資料夾/
wamp/www/你的資料夾/laravel/(底下是一堆laravel安裝的東西)
wamp/www/你的資料夾/index.php
wamp/www/你的資料夾/.htaccess
```
`laravel/public/`以下的幾個檔案和資料夾也可以順便搬過去:
```
wamp/www/你的資料夾/robots.txt
wamp/www/你的資料夾/css/
wamp/www/你的資料夾/js/
```
8. 在`wamp/www/你的資料夾/laravel/`下新增`.htaccess`並寫入以下內容以保護`laravel`資料夾不被外部讀取到
(官方是建議不要將laravel/`public/`以外的檔案放在可讓外部讀取到的網頁目錄中,如果沒辦法一定要放的話請用修改讀取權限的方式屏蔽掉`laravel/`這個資料夾)
```apache
Order deny,Allow
Deny from all
```
9. 運行以下網址看是否正確執行
如果正確的話會顯示Laravel預設的歡迎頁面,如果錯誤可能會顯示Laravel版本的找不到路徑
```
http://localhost/你的資料夾/
```
10. 運行以下幾個網址檢查看是否正確屏蔽掉`laravel/`資料夾
如果正確屏蔽掉的話會顯示`403 Forbidden`訊息
```
http://localhost/你的資料夾/laravel/
http://localhost/你的資料夾/laravel/public/
```
