- 공식 문서 : https://redis.io/docs/data-types/
- http://redisgate.kr/redis/introduction/redis_intro.php



## Strings (숫자/문자 등을 저장)

```
> set key value [EX seconds|PX milliseconds|EXAT timestamp|PXAT milliseconds-timestamp|KEEPTTL] [NX|XX] [GET]
> get key
> incr key
> incrby key increment
> decr key
> decrby key decrement
```

- 옵션
  - EX seconds : 지정한 초 이후 삭제됨
  - PX milliseconds : 지정한 밀리초 이후 삭제됨
  - EXAT timestamp : 지정한 시점(초) 이후 삭제됨
  - PXAT milliseconds-timestamp : 지정한 시점(밀리초) 이후 삭제됨
  - KEEPTTL : 만료시간 유지. 
  - NX : 겹쳐쓰기 방지. 같은 키가 없을때만 저장. 
  - XX : 키가 이미 있을때만 저장
  - GET : 새 값은 넣고 기존 값은 가져오기

## Lists (list 저장)

```
> lpush key element [element ...] 
> rpush key element [element ...]
> lpop key [count]
> rpop key [count]
> lrange key start stop
```



## Sets (중복 불가)

```
> sadd key member [member ...]
> smembers key
```



## Hashes (필드-값 쌍)

```
> hset key field value [field value ...] 
> hget key field
> hgetall key
```



## Sorted sets (score 기준으로 정렬된 set)

```
> zadd key [NX|XX] [GT|LT] [CH] [INCR] score member [score member ...]
> zrange key start stop [BYSCORE|BYLEX] [REV] [LIMIT offset count] [WITHSCORES]
> zremrangebyrank key start stop // 인덱스 start부터 end까지 삭제
```

- score은 중복이 가능하고, 중복된다면 member로 정렬됨

- 옵션

  - NX : 겹쳐쓰기 방지. 같은 키가 없을때만 저장. 

  - XX : 키가 이미 있을때만 저장

  - GT : 새 score가 현재 score보다 큰 경우에만 업데이트

  - LT : 새 score가 현재 score보다 작은 경우에만 업데이트

  - CH : score를 업데이트한 경우에만 업데이트한 member 수를 리턴

  - INCR : score를 주어진 값만큼 증가시킴
  - BYSCORE : 인덱스가 아닌 score를 조회 조건으로 사용
  - BYLEX : 인덱스가 아닌 member를 조회 조건으로 사용
  - REV : 역순으로 조회
  - LIMIT offset count : 개수 제한. BYSCORE, BYLEX 사용했을때만 사용가능. 
  - WITHSCORES : score도 함께 조회



## Streams

## Geospatial

## HyperLogLog

## Bitmaps

## Bitfields
