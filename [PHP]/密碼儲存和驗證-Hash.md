# 密碼儲存和驗證-Hash

```
*這篇是個人筆記,不保證內容完全理解正確,還是請以官方資料為準


白話講,Hash雜湊碼是用來保護使用者的密碼
在數學理論上雜湊是不可逆的計算方式(但是算法是人寫的,哪天還是可能被找出算法漏洞,像md5)
Hash可以防止資料庫外洩或是內賊時取得用戶真正的密碼,
然後進一步去盜取其他網站的資料(如果使用者使用相同帳號密碼的話)

但是目前雜湊碼有幾個問題點:
  問題一: 雜湊算法很難保證完全不會被破解,像是已經被破解的md5,sha-1
  問題二: 雜湊碼可以利用事先算好的方式建立彩虹表,利用查表的方式反查密碼

問題一的解決方式就是使用更新更嚴謹的Hash方法

問題二早期的解決方式是在密碼中加固定的鹽(salt)後再Hash
但是固定鹽的一大問題點在只要有一組Hash碼被解出來取得salt
就可以用這組鹽去建立彩虹表破解其他密碼

然後php的password_hash()方法就是為了解決這個問題
他是讓每次Hash的salt碼都不同(隨機產生)
並且將計算時間提升到每組一秒以上
(可由參數調升,調越高表示要破解時間成本越高,但是對網頁伺服器本身來說壓力也是變更大)
這表示如果要破解等於每個salt都要重新建立彩紅表
以目前的理論來說是不可能有足夠的空間去儲存這些海量的彩虹表,
因為光是salt就已經是海量的存在,而且salt幾乎不會重覆使用
這可以讓彩虹表變成要耗費大量空間儲存又幾乎等於無用的狀態

password_hash()算出來的結果會像這樣:
  $2y$10$GyD5QhZjZ1woZ2PMVIPmpO/ok12hltPhLj8HlsqRYK8KQo12tsqD6

  $2y$ 是hash方法
  10$ 是計算的重覆次數,10就是2的10次方(預設值是10,可以調高增加計算時間來增加破解難度)
  22碼的salt
  30碼的hash值

這個算法在wiki上有詳細解釋: https://en.wikipedia.org/wiki/Bcrypt


比較特別的是傳統加salt法是很怕slat讓人知道
但是這個方法是直接把算法和salt都和hash值存在一起
這就表示hash值外洩時算法和slat也會被知道
所以他的用意真的就是拖長破解整個資料庫hash值的總時間用的
因為在這種彩虹表幾乎無用的狀態下變成每組hash值都要重新去暴力破解
大大增加整個資料庫被完整破解完的時間長度
但是目標如果是針對特定的少數帳號來破解那這些帳號還是有短時間內被破解的風險(因為算法和salt都是已知的)
尤其是那些用在加密貨幣的超強算力如果被投注在破解這些特定帳號下
```

## password_hash()
http://php.net/manual/zh/function.password-hash.php

```php=
string password_hash ( string $password , int $algo [, array $options ] )
```

## password_verify()
http://php.net/manual/zh/function.password-verify.php

```php=
bool password_verify ( string $password , string $hash )
```

## password_get_info()
http://php.net/manual/zh/function.password-get-info.php

//查看哈希值的相关信息
```php=
array password_get_info ( string $hash )
```

## password_needs_rehash()
http://php.net/manual/zh/function.password-needs-rehash.php

//检查一个hash值是否是使用特定算法及选项创建的
```php=
bool password_needs_rehash ( string $hash , int $algo [, array $options ] )
```

## 參考資料

* [现代 PHP 中的密码安全性](https://www.ibm.com/developerworks/cn/web/wa-php-renewed_2/index.html)
* [bcrypt](https://en.wikipedia.org/wiki/Bcrypt)