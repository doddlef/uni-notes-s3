### Basic Data Structure
There are five basic data structures: **String**, **List**, **Set**, **Hash**, **Zset**, which are directly provided to user. They are implemented based on:
- `String`: `SDS`: simple dynamic string
- `List`: LinkedList, ZipList, QuickList
- `Hash`: Dict, ZipList
- `Set`: Dict, IntSet
- `Zset`: ZipList, SkipList

More
- [Redis Data Structures](https://redis.com/redis-enterprise/data-structures/)

### String
A **binary safe** data types, could save any type of data, including string, integer, float, picture (Base64 encoded, or url of picture), serialised object.

| commands             |                                      |
| -------------------- | ------------------------------------ |
| SET key value        | set the value of the key             |
| SETNX key value      | only set when key not exist          |
| GET key              |                                      |
| MSET k1 v1 k2 v2 ... |                                      |
| MGET k1 k2 ...       |                                      |
| STRLEN key           | length of stored string              |
| EXISTS key           |                                      |
| INCR/DECR key        | increase/decrease stored number by 1 |
- can used to store session, token, url, serialised object (save more space than `Hash`)
- store count of users' request or visiting time

### List
Redis implements a `bi-direction linked list`

| commands             |                                                          |
| -------------------- | -------------------------------------------------------- |
| RPUSH key v1 v2 ...  | push elements to the tail (right)                        |
| LPUSH key v1 v2 ...  |                                                          |
| LSET key index value | set the list\[index] to value                            |
| LPOP/RPOP key        | pop the value from left/right                            |
| LLEN key             | length of list                                           |
| LRANGE key start end | get the sub list between start and end (including)       |
|                      | using -1, -2, ... for the last, second last, ... element |
- can implement pagination using `LRANGE`
- display range of data, latest event, ...

### Hash
a **`field-value`** pair table of String, used to store object, just like a `HashMap`

| commands                    |                           |
| --------------------------- | ------------------------- |
| HSET key field value        | set the value of field    |
| HSETNX key field value      | only when field not exist |
| HGET key field              |                           |
| HGETALL key                 |                           |
| HEXISTS key field           |                           |
| HDEL key f1 f2 ...          |                           |
| HINCRBY key field increment |                           |
- store object data: user, product, ...

### Set
un-order but unique collection

| commands                      |                               |
| ----------------------------- | ----------------------------- |
| SADD key e1 e2 ...            |                               |
| SMEMBERS key                  | all elements in a set         |
| SCARD key                     | number of elements            |
| SISMEMBER key element         | contains?                     |
| SINTER k1 k2                  | `and`                         |
| SINTERSTORE destination k1 k2 | store the result in a new key |
| SUNION k1 k2                  |                               |
| SUNIONSTORE destination k1 k2 |                               |
| SDIFF k1 k2                   |                               |
| SDIFFSTORe destination k1 k2  |                               |
| SPOP key count                | pop and get random elements   |
| SRANDMEMBER key count         | random elements inside a set  |
- store unique data
- when need do `inter`, `union` or `diff`, shared friends, ...
- need to get random elements

### Sorted Set
similar to `Set`, but adding a `score` for elements to order by it

| commands                          |                            |
| --------------------------------- | -------------------------- |
| ZADD key score1 element1 ...      |                            |
| ZCARD key                         |                            |
| ZSCORE key element                |                            |
| ZINTERSTORE destination key1 key2 |                            |
| ...                               |                            |
| ZRANGE key start end              |                            |
| ZRANK key element                 | get the rank of an element |

### JSON
`JSON.SET`: set a key with a JSON value:
	`JSON.SET student:alice $ '{"name": "alice", "age":10}'`
the `$` indicate the **path** to the value in the document

`JSON.STRLEN`: will give the length of a string, and `JSON.STRAPPEND` will append another string to it

`JSON.DEL` will deletes any JSON value specified by `path`

