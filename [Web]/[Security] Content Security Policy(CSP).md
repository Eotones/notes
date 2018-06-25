# Content Security Policy (CSP) 筆記

###### tags: `Security`, `HTTP Header`

**Content Security Policy (CSP) 內容安全政策**

**Content Security Policy**是寫給**瀏覽器**看的
他寫在從伺服端回應過來網頁的**HTTP Header**裡
主要用來**限制**一個網頁外部的語法來源(例如:css,js(ajax,ws),webfont,img,video,iframe等等)
還有一部份是禁止HTML行內的JS或CSS運作
以及限制`<form>`表單的指向網址

過濾XSS語法是伺服器端語言的工作(例如PHP)
如果不幸過濾失敗的話**CSP**的功能可以**阻止**惡意語法在瀏覽器端運作
算是多一道擋XSS的防線
(要注意是規則要寫對才能起作用,寫法限制太寬鬆還是會阻止不了)


### 基本範例

* **CSP**可以完全限制外部連入的檔案和行內語法(最安全),這是預設全部阻擋的寫法:
```
content-security-policy: default-src 'none';
```

* 或是以**白名單**的形式允許信任的外部來源:
```
content-security-policy: default-src 'none'; script-src 'self' https://ajax.googleapis.com;
```

建議可以先用瀏覽器的開發工具(F12)去看看Facebook和Twitter的`content-security-policy`寫法

```md
1. Chrome瀏覽器開Twitter
2. F12 --> Network --> F5重新載入網頁 --> 
3. 拉到最上面選取第一個載入的檔案(主網頁檔) --> 
4. Headers --> Response Headers --> content-security-policy
```

### 本頁主要資料來源:

* [Content-Security-Policy - HTTP | MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) - 這個很重要,建議看完
* [Content Security Policy - Wikipedia](https://en.wikipedia.org/wiki/Content_Security_Policy)


:::warning
有實測一下`default-src 'none';`無法阻止Tampermomkey這類瀏覽器插件的腳本
(以前運作方式不同似乎可以用CSP擋,但是現在實測過是無法阻擋了)
也有一些瀏覽器插件可以直接停用CSP
所以CSP的功能應該沒辦法拿來阻擋想用腳本修改網頁的人
:::


## 各語言寫法

### 原始HTTP Header:
HTTP Header是以換行當成區格,
要注意所有**CSP內容**要輸出成**同一行字串**

```
content-security-policy: default-src 'none'; img-src 'self' data:;
```

### Apache (.htaccess)
```apache
<IfModule mod_headers.c>
Header set Content-Security-Policy " \
    default-src 'none'; \
    img-src 'self' data:; \
"
</IfModule>
```

### PHP
```php
<?php
header("Content-Security-Policy: default-src 'none'; img-src 'self' data:;");
?>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>test</title>
</head>
<body>
    <p>test</p>
    <img src="example.jpg">
</body>
</html>
```

### HTML
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta http-equiv="Content-Security-Policy" content="default-src 'none'; img-src 'self' data:;">
    <title>test</title>
</head>
<body>
    <p>test</p>
    <img src="example.jpg">
</body>
</html>
```


## 指令 Directives

* `default-src` - **預設值**,以下種類未設定時會採用`default-src`的設定值
* `img-src` - `<img src="...">`
* `media-src` - `<video>` `<audio>` `<source>` `<track>`
* `style-src` - `<link rel="stylesheet" href="style.css">`
* `script-src` - `<script>`
* `manifest-src` - **icon圖標** `<link rel="manifest" href="mainfest.json">`
* `object-src` - `<object>` `<embed>` `<applet>`
* `frame-src` - `<iframe>`
* `frame-ancestors` -  `<frame>`, `<iframe>`, `<object>`, `<embed>`, or `<applet>`
* `worker-src` - `Worker()`
* `child-src` - `<iframe>` `Worker()` *已棄用,請改用上面兩個*
* `form-action` - `<form>`
* `font-src` - `@font-face`
* `connect-src` - `XMLHttpRequest()` `WebSocket()` `EventSource()` `sendBeacon()` `fetch()`
    * ajax連外部.json檔要改這一個
    * websocket
* `block-all-mixed-content;` - 禁止https/http混合內容
* `report-uri` - 當用戶端違反CSP時,回傳給server的接收網址(HTTP POST json格式)


## 語法 Syntax

CSP以**白名單**的形式運作
建議以 `default-src 'none';` 或是 `default-src 'self';`
當成起頭再來設定細部選項,以提高安全性(避免遺漏)

* `'none'` - 全部不允許
* `'self'` - 允許同網域
* `指定網域` < host-source > - 指定允許的網域,以空格分開
    * 包含或不包含`http://`,`https://`前綴的網址,可加`*`萬用字元 
    * `example.com`
    * `*.example.com`
    * `https://example.com`
    * `https://*.example.com`
    * `wss://example.com` - 測試過`wss://`前綴不能省略一定要加
    * `wss://*.example.com`
* < scheme-source >
    * `http:`
    * `https:`
    * `data:`
    * `mediastream:`
    * `blob:`
    * `filesystem:`
* `*` - 全部允許(儘量不要用)
* `'unsafe-inline'` - 允許html行內css或js
    * 最常出現XSS攻擊語法的地方就是inline,所以建議將自有的JS,CSS語法全寫成獨立檔案,最好能不要開啟這個權限
* `'unsafe-eval'` - 允許`eval()`
    * `eval()`是將字串重新轉回成可執行的程式碼,本身就是高風險的語法,不建議開啟權限
* `'nonce-<base64-value>'`
* `'<hash-algorithm>-<base64-value>'`

最後兩個驗證碼比對用法請參考: [CSP: script-src - HTTP | MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src)

## 常用白名單範例(以Apache .htaccess寫法為例)

* 常用的外部網站檔案用英文關鍵字丟Google都會有人寫過,不會寫的話可以用Google找比較快(通常寫法也會比自己寫的完整)

### Google Analytics

```apache
<IfModule mod_headers.c>
Header set Content-Security-Policy " \
    default-src 'self'; \
    script-src 'self' https://www.google-analytics.com https://www.googletagmanager.com; \
    img-src 'self' data: https://www.google-analytics.com; \
"
</IfModule>
```

### Google CND (jQuery之類的CDN外連.js)

```apache
<IfModule mod_headers.c>
Header set Content-Security-Policy " \
    default-src 'self'; \
    script-src 'self' https://ajax.googleapis.com; \
"
</IfModule>
```
```html
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
```

### Youtube

```apache
<IfModule mod_headers.c>
Header set Content-Security-Policy " \
    default-src 'self'; \
    frame-src 'self' https://www.youtube.com; \
"
</IfModule>
```
```html
<iframe width="560" height="315" src="https://www.youtube.com/embed/vtwiz6P1rdw" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
```

### OBS Studio (Browser Source)
如果你的網頁有要嵌入OBS中,記得要加`data:`才能修改自訂CSS

```apache
<IfModule mod_headers.c>
Header set Content-Security-Policy " \
    default-src 'self'; \
    style-src 'self' 'unsafe-inline' data:; \
"
</IfModule>
```

## 其他CSP以外的安全性設定
### CSP以外增加安全性的Header(以Apache .htaccess寫法為例)

```apache
# =========== Header ===========
<IfModule mod_headers.c>
    # 唯有當符合同源政策下，才能被嵌入到 frame 中。(防止網站被外部釣魚網站嵌入)
    Header always append X-Frame-Options SAMEORIGIN
    
    # 拒絕 MIME 類型錯誤的回應 (js,css)
    Header set X-Content-Type-Options "nosniff"
    
    # 將所有Cookie設為HttpOnly
    Header always edit Set-Cookie (.*) "$1; HTTPOnly; Secure"
    
    #隱藏PHP版本
    Header always unset X-Powered-By
</IfModule>
# ==============================


# ========= 禁止訪問檔案 =========
# 這些檔案不應該放在外部網路能讀取到的地方
# 如果你還是上傳了這些檔案請把他給擋掉外部讀取
# 不然這會很危險
# .git可以把你所有程式碼還源
# composer.json, package.json可以知道你用了哪些package和版本
# 如果你用了舊版本沒更新,可能就會被找到漏洞

RedirectMatch 404 /\.git

<Files composer.json>
	order allow,deny
	deny from all
</Files>
<Files composer.lock>
	order allow,deny
	deny from all
</Files>
<Files .gitignore>
	order allow,deny
	deny from all
</Files>
<Files .htacces>
	order allow,deny
	deny from all
</Files>
<Files .htaccess.sample>
	order allow,deny
	deny from all
</Files>
<Files .php_cs>
	order allow,deny
	deny from all
</Files>
<Files .travis.yml>
	order allow,deny
	deny from all
</Files>
<Files CHANGELOG.md>
	order allow,deny
	deny from all
</Files>
<Files CONTRIBUTING.md>
	order allow,deny
	deny from all
</Files>
<Files CONTRIBUTOR_LICENSE_AGREEMENT.html>
	order allow,deny
	deny from all
</Files>
<Files COPYING.txt>
	order allow,deny
	deny from all
</Files>
<Files Gruntfile.js>
	order allow,deny
	deny from all
</Files>
<Files LICENSE.txt>
	order allow,deny
	deny from all
</Files>
<Files LICENSE_AFL.txt>
	order allow,deny
	deny from all
</Files>
<Files nginx.conf.sample>
	order allow,deny
	deny from all
</Files>
<Files package.json>
	order allow,deny
	deny from all
</Files>
<Files php.ini.sample>
	order allow,deny
	deny from all
</Files>
<Files README.md>
	order allow,deny
	deny from all
</Files>
# ===============================


# ========隱藏目錄下所有檔案========
<IfModule mod_autoindex.c>
	Options -Indexes
</IfModule>
# ===============================
```


### 防止CDN來源的檔案被CDN業者篡改

這個功能在[Bootstrap](https://getbootstrap.com/)官網就能看到實例
利用`integrity`中的雜湊碼來檢查檔案是否被偷改過
相同的檔案雜湊碼每次算出來都會一樣
被偷改過的檔案雜湊碼則會不同(即使只改一個字雜湊碼也會完全不一樣)
雜湊碼不同時則檔案不會載入運行

```html
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.0/css/bootstrap.min.css" integrity="sha384-9gVQ4dYFwwWSjIDZnLEWnxCjeSWFphJiwGPXr1jddIhOegiu1FwO5qRGvFXOdJZ4" crossorigin="anonymous">

<script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.0/umd/popper.min.js" integrity="sha384-cs/chFZiN24E4KMATLdqdvsezGxaGsi4hLGOzlXwp5UZB1LY//20VyM2taTB4QvJ" crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.0/js/bootstrap.min.js" integrity="sha384-uefMccjFJAIv6A+rW+L4AHf99KvxDjWSu1z9VI8SKNVmz4sk7buKt/6v9KI65qnm" crossorigin="anonymous"></script>
```

### 防止點網址開新視窗時原網頁被切換篡改

```html
<a rel="noopener noreferrer" target="_blank" href="http://example.com/">連結</a>
```