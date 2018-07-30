# 還原github clone下來的Laravel檔案

參考資料:
* [cloning laravel project from github](https://stackoverflow.com/questions/38602321/cloning-laravel-project-from-github)
* [如何重建由GitHub下載的Laravel專案?](https://oomusou.io/laravel/laravel-clone-from-github/)

## 須要還原的檔案

```
/vendor
/node_modules
.env
```

指令:

```shell
$ git clone https://github.com/Eotones/laravel-markdown.git
$ cd laravel-markdown

# 還原vendor目錄
$ composer install

# 還原node_modules目錄
$ npm install

# 將 .env.example 複製並改名為 .env
$ copy .env.example .env
# 複製完後開啟 .env 檔 修改 database name 和 password

# 產生新的key
$ php artisan key:generate

# 重建資料庫
$ php artisan migrate

$ php artisan serve
# Go to localhost:8000
```

## 資料庫遷移

* 資料庫遷移的詳細用法可以參考這一篇: [Laravel 5.6 数据库: 迁移](https://laravel-china.org/docs/laravel/5.6/migrations/1400)