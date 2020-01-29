# redis

* KEYS
  * KEYS pattern
    * e.g. KEYS *name*, KEYS *
* SCAN
  * SCAN cursor [MATCH pattern] [COUNT count]
    * e.g.
      * scan 0
      * scan 17
      * scan 0 MATCH *name*
* Strings
  * get, set key value
    * e.g.
      * set key4 value
      * get key4

## 참고
* https://redis.io/commands/keys
* http://redisgate.kr/redis/command/get.php
