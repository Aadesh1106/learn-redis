```markdown
# Redis Study Journal

This journal documents my study journey through Redis. The notes below cover the fundamentals and various commands for working with Redis, including strings, lists, sets, sorted sets, HyperLogLog, hashes, transactions, Pub/Sub, scripts, geospatial operations, and benchmarking. All information presented here is based solely on what I have learned.

---

## Why Study Redis

Redis is an open-source, in-memory NoSQL data store used primarily as an application cache or quick-response database. It is known for its speed and is ideal for building fast applications that require rapid data access and low latency. Redis supports various data structures such as strings, hashes, lists, sets, sorted sets, bitmaps, HyperLogLog, geospatial indexes, and streams. This versatility allows developers to model data effectively and choose the right data structure for each task.

---

## Redis Strings

- **Overview**:  
  Redis functions as an in-memory data structure store. It is used as a NoSQL database, distributed cache, and message broker. It supports data structures like strings, hashes, lists, sets, sorted sets, bitmaps, HyperLogLog, and geospatial indexes.

- **Starting the Redis Server**:  
  If the `redis-server` command does not work, try these commands:
  ```bash
  sudo systemctl start redis
  sudo systemctl status redis-server
  sudo systemctl start redis
  sudo systemctl enable redis-server
  ```

- **Redis CLI**:  
  The `redis-cli` is used as the client to interact with the Redis server.

- **Key-Value Operations**:
  - Set a key-value pair:
    ```bash
    set name aadesh
    ```
    Here, `name` is the key and `aadesh` is the value.
    
  - Retrieve the value of a key:
    ```bash
    get name
    ```
    
  - Retrieve a range of characters from a value:
    ```bash
    getrange key startindex endindex
    ```
    
  - Set multiple key-value pairs in a single command:
    ```bash
    mset key value [key value ...]
    ```
    
  - Retrieve values for multiple keys:
    ```bash
    mget key1 key2 ...
    ```
    
  - Get the length of the value stored at a key:
    ```bash
    strlen key
    ```
    (Returns 0 if the string is not present.)
    
  - Using integers:
    ```bash
    set count 1
    get count
    ```
    
  - Increment and decrement operations:
    ```bash
    incr count          # Increments count by one
    incrby count 10     # Increments count by 10
    decrby count 10     # Decrements count by 10
    ```
    
  - Increment a float value (as per my notes):
    ```bash
    incrby <datatype> key value
    ```
    
  - Expire a key after a given number of seconds:
    ```bash
    expire key time
    ```
    
  - Check the remaining time-to-live (TTL) of a key:
    ```bash
    ttl key
    ```

---

## Redis Lists

- **General Commands**:
  - List all keys:
    ```bash
    keys *
    ```
  - Remove all keys:
    ```bash
    flushall
    ```

- **List Operations**:
  - **LPUSH**: Add an element to the beginning of the list (similar to pushing onto a stack):
    ```bash
    lpush key value
    ```
    
  - **LRANGE**: Retrieve elements from the list:
    ```bash
    lrange key start stop
    ```
    To view the full list:
    ```bash
    lrange key 0 -1
    ```
    
  - **RPUSH**: Add an element to the end of the list:
    ```bash
    rpush key value
    ```
    
  - **LLEN**: Get the length of the list:
    ```bash
    llen key
    ```
    
  - **LPOP**: Remove and return the first element of the list:
    ```bash
    lpop key
    ```
    
  - **RPOP**: Remove and return the last element of the list:
    ```bash
    rpop key
    ```
    
  - **RPUSHX**: Append an element to an existing list (returns 0 if the list does not exist):
    ```bash
    rpushx key value
    ```
    
  - **BLPOP**: Remove and return the first element of the list with a blocking timeout:
    ```bash
    blpop key timeout
    ```
    For example:
    ```bash
    blpop country 3
    ```
    
  - **SORT**: Sort the list elements in ascending order:
    ```bash
    sort key ALPHA
    ```
    To sort in descending order, use the appropriate keyword (e.g., `desc`).

---

## Redis Sets

- **Basic Operations**:
  - **SADD**: Add a new member to a set:
    ```bash
    sadd key value
    ```
    (Multiple values can be added using the same key.)
    
  - **SCARD**: Return the number of elements in the set:
    ```bash
    scard key
    ```
    
  - **SMEMBERS**: Retrieve all the members of the set:
    ```bash
    smembers key
    ```
    
  - *Note*: Redis sets do not allow duplicate values. Adding a duplicate returns 0.

- **Set Operations**:
  - **SDIFF**: Return the difference between sets (elements present in one set but not in the other):
    ```bash
    sdiff key1 key2
    ```
    
  - **SINTER**: Return the intersection of sets:
    ```bash
    sinter key1 key2
    ```
    
  - **SDIFFSTORE / SINTERSTORE / SUNIONSTORE**: Store the result of the set difference, intersection, or union into a destination key:
    ```bash
    sdiffstore destination key1 key2
    ```
    (Similarly for `sinterstore` and `sunionstore`.)
    
  - **SUNION**: Merge the values of two sets:
    ```bash
    sunion key1 key2
    ```

---

## Sorted Sets

- **Overview**:  
  Sorted sets manage data as key-score pairs.

- **Commands**:
  - **ZADD**: Add a member with an associated score:
    ```bash
    zadd key withscore value
    ```
    
  - **ZRANGE**: Retrieve elements in order:
    ```bash
    zrange key 0 -1
    ```
    To include scores:
    ```bash
    zrange key 0 -1 withscores
    ```
    
  - **ZCARD**: Get the total number of elements in the sorted set:
    ```bash
    zcard key
    ```
    
  - **ZCOUNT**: Count the number of elements within a score range:
    ```bash
    zcount key -inf +inf
    zcount key 0 4
    ```
    
  - **ZREM**: Remove a specific element:
    ```bash
    zrem key value
    ```
    
  - **ZREVRANGE**: Retrieve elements in descending order:
    ```bash
    zrevrange key 0 -1 withscores
    ```
    
  - **ZREVRANGEBYSCORE**: Retrieve elements by score in descending order:
    ```bash
    zrevrangebyscore key max min withscores
    ```
    
  - **ZINCRBY**: Increment the score of a member:
    ```bash
    zincrby key increment value
    ```
    
  - **ZREMRANGEBYSCORE**: Remove elements within a given score range:
    ```bash
    zremrangebyscore key min max
    ```
    
  - **ZREMRANGEBYRANK**: Remove elements by their rank (index):
    ```bash
    zremrangebyrank key start stop
    ```
    *(Rank refers to the index positions in the sorted set.)*

---

## HyperLogLog

- **Overview**:  
  HyperLogLog is a probabilistic data structure used for counting unique values (e.g., IP addresses or email addresses visiting a website).

- **Commands**:
  - **PFADD**: Add one or more elements to the HyperLogLog:
    ```bash
    pfadd key value1 value2 value3 value4 value5
    ```
    
  - **PFCOUNT**: Get the approximate count of unique elements:
    ```bash
    pfcount key
    ```
    
  - **PFMERGE**: Merge multiple HyperLogLogs into one:
    ```bash
    pfmerge mergehll key1 key2
    ```

---

## Redis Hashes

- **Commands**:
  - **HSET**: Set the value of a field in a hash:
    ```bash
    hset myhash key value
    ```
    
  - **HKEYS**: Retrieve all field names in the hash:
    ```bash
    hkeys keyname
    ```
    
  - **HVALS**: Retrieve all field values in the hash:
    ```bash
    hvals keyname
    ```
    
  - **HGETALL**: Retrieve all field-value pairs in the hash:
    ```bash
    hgetall key
    ```
    
  - **HEXISTS**: Check if a specific field exists in the hash:
    ```bash
    hexists key field
    ```
    
  - **HMSET**: Set multiple fields in a hash at once:
    ```bash
    hmset key field1 value1 field2 value2 ...
    ```
    
  - **HINCRBY**: Increment the integer value of a field:
    ```bash
    hincrby myhash field increment
    ```
    
  - For floating-point increments (as per my notes):
    ```bash
    hincrbydatatype myhash age value
    ```
    *(Examples: 2.5, 5.6, 1.4, etc.)*
    
  - **HDEL**: Delete a field from the hash:
    ```bash
    hdel myhash field
    ```
    
  - **HSTRLEN**: Get the length of the value of a field:
    ```bash
    hstrlen myhash field
    ```
    
  - **HSETNX**: Set the value of a field only if it does not exist:
    ```bash
    hsetnx key newkey value
    ```

---

## Redis Transactions

- **Overview**:  
  Redis transactions allow multiple commands to be executed in a single step using the `MULTI` command.

- **Example**:
  ```bash
  set name oshabbir
  set a 2
  get a
  set b 3
  get b
  exec
  ```
  All commands are queued until `exec` is called. Use `discard` to cancel the transaction. The `watch` command can also be used to monitor keys for changes.

---

## Redis Pub/Sub

- **Overview**:  
  Pub/Sub (publish/subscribe) enables communication between processes through channels.

- **Commands**:
  - Subscribe to a channel:
    ```bash
    subscribe channelname
    ```
    
  - Publish a message to a channel:
    ```bash
    publish channelname message
    ```
    
  - Subscribe using patterns:
    ```bash
    psubscribe news* h?llo b[ai]ll
    ```
    
  - Additional Pub/Sub commands:
    ```bash
    pubsub channels       # Lists active channels
    pubsub numbsub news   # Shows the number of subscribers for the 'news' channel
    pubsub numpat         # Displays the number of pattern subscriptions
    ```

---

## Redis Scripts

- **Overview**:  
  Redis supports scripting using the `EVAL` command to execute Lua scripts.

- **Examples**:
  - Set a key using a script:
    ```bash
    eval "redis.call('set', KEYS[1], ARGV[1])" 1 name shabbir
    get name  # Returns 'shabbir'
    ```
    
  - Set multiple keys using a script:
    ```bash
    eval "redis.call('mset', KEYS[1], ARGV[1], KEYS[2], ARGV[2])" 2 name last_name varun tharun
    mget name last_name  # Returns 'varun' and 'tharun'
    ```
    
  - Working with hashes and sorted sets:
    ```bash
    hmset country_cap "New Delhi" USA "sans" ...
    zadd country 1 Italy 2 India 3 USA
    zrange country 0 -1  # Returns all country key-value pairs
    ```
    
  - Script to retrieve country capitals:
    ```bash
    eval "local order = redis.call('zrange', KEYS[1], 0, -1); redis.call('hmget', KEYS[2], unpack(order));" 2 country country_cap
    ```
    
  - Alternatively, load a script to obtain its hexadecimal code:
    ```bash
    script load "local order = redis.call('zrange', KEYS[1], 0, -1); return redis.call('hmget', KEYS[2], unpack(order));"
    evalsha <hexadecimal_code> 2 country country_cap
    script exists <hexadecimal_code>  # Checks if the script exists
    ```

---

## Redis Geospatial

- **Overview**:  
  Redis provides geospatial commands to store and query location-based data.

- **Commands**:
  - **GEOADD**: Add geographic coordinates with a member:
    ```bash
    GEOADD maps 72.585022 23.033863 Ahmedabad
    GEOADD maps 72.8775426 19.076050 Mumbai 77.580643 12.9772442 Banglore
    ```
    
  - Retrieve all members:
    ```bash
    zrange maps 0 -1
    ```
    
  - **GEOPOS**: Get the longitude and latitude of a member:
    ```bash
    GEOPOS maps Pune
    ```
    
  - **GEODIST**: Calculate the distance between two members:
    ```bash
    GEODIST maps Mumbai Pune
    GEODIST maps Mumbai Pune km  # Units: km, m, mi, ft
    ```
    
  - **GEORADIUS**: Retrieve members within a specified radius:
    ```bash
    GEORADIUS maps 73.856255 18.516724 500 km
    GEORADIUS maps 73.856255 18.516724 500 km withcoord
    GEORADIUS maps 73.856255 18.516724 500 km withcoord withdist
    GEORADIUS maps 73.856255 18.516724 500 km withcoord withdist withhash
    ```
    
  - **GEORADIUSBYMEMBER**: Retrieve members within a radius from a specific member:
    ```bash
    GEORADIUSBYMEMBER maps Ahmedabad 500 km
    GEORADIUSBYMEMBER maps Ahmedabad 1500 km desc
    GEORADIUSBYMEMBER maps Ahmedabad 1500 km asc
    ```

---

## Redis Benchmark

- **Commands**:
  - Run a benchmark with 1000 requests:
    ```bash
    redis-benchmark -n 1000
    ```
    
  - Run a benchmark with 1000 requests and 10,000 bytes per request:
    ```bash
    redis-benchmark -n 1000 -d 10000
    ```
    
  - Run a benchmark with 1000 requests, 10,000 bytes per request, and 20 parallel clients:
    ```bash
    redis-benchmark -n 1000 -d 10000 -c 20
    ```

---

*End of Journal*
```
