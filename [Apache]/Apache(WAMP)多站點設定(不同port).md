# Apache(WAMP)多站點設定(不同port)

**httpd.conf** 最後一行新增:

```apache
#自訂
Listen 8080
<VirtualHost *:8080>
    ServerName localhost:8080
    DocumentRoot "d:/wamp/www_2018/"
    
</VirtualHost>
<Directory "d:/wamp/www_2018/">
    AllowOverride all
    
    # 僅允許本地訪問
    Require local

    # 禁止所有訪問
    #Require all denied

    # 允許所有訪問
    #Require all granted
</Directory>
```

設定完後重開Apache
這樣就可以同時執行兩個網站(可以根據自己需求開更多個,記得port要選沒有在使用的,選到被佔用的port可能會開不起來)
還有因為是開發用途,為確保安全所以只限定本地訪問

* http://localhost/ (d:/wamp/www/)
* http://localhost:8080/ (d:/wamp/www_2018/)