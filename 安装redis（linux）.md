#  安装redis（linux） #

 1. 在root用户下解压：命令tar -xzvf file.tar.gz
 2. 进入redis目录分别执行make 和 makeinstall或make(制定目录)PREFIX=XXX install  如果不指定他会默认配置到usr/local/bin 
 3. 进入/usr/local/bin目录下执行 redis-server /xx/xx/redis.conf(表示打开redis服务器端)
 4. 重新打开窗口执行redis-cli  -p 6379
  
 
## key ##
dump key 序列化给定key 返回被序列化的值

    127.0.0.1:6379> set haha heihei
    OK
    127.0.0.1:6379> dump haha
    "\x00\x06heihei\x06\x00z&^\x0c]\xf0\xbf\xdf"

exists key   检查给定key是否存在 

    redis> SET db "redis"
    OK
    redis> EXISTS db
    (integer) 1
    redis> DEL db
    (integer) 1
    redis> EXISTS db
    (integer) 0
expire key seconds 给key指定过期时间  

    127.0.0.1:6379> set baidu "www.baidu.com"
    OK
    127.0.0.1:6379> expire baidu 30
    (integer) 1
    127.0.0.1:6379> ttl baidu
    (integer) 20
    127.0.0.1:6379> expire baidu 3000
    (integer) 1
    127.0.0.1:6379> ttl baidu
    (integer) 2994

keys pattern 查找所有给定模式pattern的key

    127.0.0.1:6379> mset one 1 two 2 three 3 four 4
    OK
    127.0.0.1:6379> keys  *
    1) "two"
    2) "three"
    3) "jaj"
    4) "baidu"
    5) "one"
    6) "four"
    7) "haha"
migrate 将key原子性的从当前实例传达到目标实例的指定数据库上，当前实例的key会被删掉

    -bash-4.1# redis-cli -p 6379
    127.0.0.1:6379> keys
    (error) ERR wrong number of arguments for 'keys' command
    127.0.0.1:6379> keys 
    (error) ERR wrong number of arguments for 'keys' command
    127.0.0.1:6379> key
    (error) ERR unknown command 'key'
    127.0.0.1:6379> get haha
    "heihei"
    127.0.0.1:6379> migrante 127.0.0.1 7777 haha 0 1000
    (error) ERR unknown command 'migrante'
    127.0.0.1:6379> migrate 127.0.0.1 7777 haha 0 1000
    OK
    127.0.0.1:6379> exists haha
    (integer) 0
    127.0.0.1:6379> 
    
    
    -bash-4.1# redis-cli -p 7777
    127.0.0.1:7777> get haha
    "heihei"
    
move key db   将当前数据库的 key 移动到给定的数据库 db 当中。
   
     127.0.0.1:6379> set ha hei
    OK
    127.0.0.1:6379> get ha
    "hei"
    127.0.0.1:6379> move ha 1
    (integer) 1
    127.0.0.1:6379> select 1
    OK
    127.0.0.1:6379[1]> exists ha
    (integer) 1
    127.0.0.1:6379[1]> exists hh
    (integer) 0
    127.0.0.1:6379[1]> select 0
    OK
    127.0.0.1:6379> get ha
    (nil)
    
persist key 
移除给定key的生存时间，将这个key从具有生存时间转化为持久的不带生存时间(不过期)的key

    127.0.0.1:6379> set h "hello"
    OK
    127.0.0.1:6379> expire h 12
    (integer) 1
    127.0.0.1:6379> ttl h
    (integer) 6
    127.0.0.1:6379> persist h
    (integer) 0
    127.0.0.1:6379> ttl h
    (integer) -2

pttl 当key不存在时，返回-2
     当key存在但没有设置剩余生存时间时，返回-1
     否则返回剩余生存时间
   

       set aa aaa
    OK
    127.0.0.1:6379> pexpire aa 90909
    (integer) 1
    127.0.0.1:6379> pttl aa
    (integer) 82985

randomkey 从当前数据库中随机返回一个key.

    # 数据库不为空
    
    redis> MSET fruit "apple" drink "beer" food "cookies"   # 设置多个 key
    OK
    redis> RANDOMKEY
    "fruit"
    redis> RANDOMKEY
    "food"
    redis> KEYS *    # 查看数据库内所有key，证明 RANDOMKEY 并不删除 key
    1) "food"
    2) "drink"
    3) "fruit"
    # 数据库为空
    redis> FLUSHDB  # 删除当前数据库所有 key
    OK
    redis> RANDOMKEY
    (nil)
    
renama key newkey 将key改名为newkey

    127.0.0.1:6379> set msg "message"
    OK
    127.0.0.1:6379> rename msg greeting
    OK
    127.0.0.1:6379> exist msg
    (error) ERR unknown command 'exist'
    127.0.0.1:6379> exists msg
    (integer) 0
    127.0.0.1:6379> exists greeting 
    (integer) 1
    127.0.0.1:6379> set aa bb
    OK
    127.0.0.1:6379> set cc dd
    OK
    127.0.0.1:6379> rename aa cc
    OK
    127.0.0.1:6379> get aa
    (nil)
    127.0.0.1:6379> get cc
    "bb"
    127.0.0.1:6379> get cc
type key 返回key所存储的值得类型

     127.0.0.1:6379> set msg "message"
    OK
    127.0.0.1:6379> rename msg greeting
    OK
    127.0.0.1:6379> exist msg
    (error) ERR unknown command 'exist'
    127.0.0.1:6379> exists msg
    (integer) 0
    127.0.0.1:6379> exists greeting 
    (integer) 1
    127.0.0.1:6379> set aa bb
    OK
    127.0.0.1:6379> set cc dd
    OK
    127.0.0.1:6379> rename aa cc
    OK
    127.0.0.1:6379> get aa
    (nil)
    127.0.0.1:6379> get cc
    "bb"
    127.0.0.1:6379> get cc




