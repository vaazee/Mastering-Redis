# Mastering-Redis 

# My notes 

```
127.0.0.1:6379> SCRIPT LOAD "for i=1,ARGV[1] do local key='test:'..i redis.call('SET', key, 'value='..i) end"
"27e7dfbde55d6dba69bdc498ead38c29827a9cec"
127.0.0.1:6379> EVALSHA 27e7dfbde55d6dba69bdc498ead38c29827a9cec
(error) ERR wrong number of arguments for 'evalsha' command
127.0.0.1:6379> EVALSHA 27e7dfbde55d6dba69bdc498ead38c29827a9cec 0 10
(nil)

```

```
./redis-server --dbfilename replicant.rdb --port 6380
redis-cli -p 6380
127.0.0.1:6380> replicaof 127.0.0.1 6379
OK
127.0.0.1:6380> dbsize
(integer) 0
127.0.0.1:6380> dbsize
(integer) 100002


```
## Sharding  (Chapter 6)

- Three techniques 
    - Client-side sharding
    - Proxy routing
    - query routing, one that is used in redis cluster 

### Client-side sharding 

- Range Partitioning - dividing the keys into select partitions with each range assigned to one redis instance. We can use a bitmap to make this easier - assign right bits to each key to map to a particular instance 

- List partitioning - The Redis instances are assigned a list of values that a key must match to be assigned to that partition by the client

- Hash partitioning - a hash alg is applied to a redis key and a modulo (%) is applied to a result. It is then used to assign a instance to the key

- Composite partitioning - a combination of the above will be used. Redis cluster uses a form of composite partitioning called consistent hashing that combines features of the Hash and List partitioning to calculate a key's instance or node in Redis cluster terminology. Called a hash slot, it is the key's CRC16 hash value and then a computation of a modulo using 16384. The specific algorithm used by Redis cluster to calculate the hash slots for a key is simply the cyclic redundancy check (CRC) using a polynomial length of 17-bits or CRC16 with a theoretical maximum number of cluster nodes 16,284 with each node being a running Redis instance. To effectively use Redis cluster's consistent hashing algorithm, a minimal three Redis nodes are necessary in your Redis cluster. For a three-node Redis cluster, the hash slots are assigned with the following:

    - The first master having hash slots 0 to 5500
    - The second master node being assigned hash slots 5501 to 11,000
    - The third master having the remainder of 11,001 to 16,384 hash slots.The actual hash slot for the key is calculated as the modulo of CRC16 of the key divided by 16384 like this: `HASH_SLOT = CRC16(key) modulo 16384`

### Twemproxy

Twemproxy supports twelve different hash functions including md5, crc16, two versions of crc32, four variants of the Fowler-Noll-Vo (FNV), among others with the default being a fnv1a_64 hash functions.

```

❯ pwd
/Users/vrajendr/code/github/vaazee/twemproxy/src

❯ pwd
/Users/vrajendr/code/github/vaazee/twemproxy/src/conf

…~/c/g/va/twemproxy/sr/conf   master ?1  04:53:29 PM
❯ cat nutcracker.yml
alpha:
  listen: 127.0.0.1:22121
  hash: fnv1a_64
  distribution: ketama
  auto_eject_hosts: true
  redis: true
  server_retry_timeout: 2000
  server_failure_limit: 1
  servers:
   - 127.0.0.1:6379:1


…~/c/g/va/twemproxy/sr/conf   master ?1  04:53:31 PM

❯ redis-cli -p 22121
127.0.0.1:22121> get foo
"bar"


```

### Redis Cluster


# From the master repo....
This is the code repository for [Mastering-Redis] (https://www.packtpub.com/big-data-and-business-intelligence/mastering-redis?utm_source=github&utm_medium=repository&utm_campaign=9781783988181), published by Packt. It contains all the supporting project files necessary to work through the book from start to finish.

## Chapter-wise software and OS specifications

* Chapter number: 1 to 10, Software required: Redis >= 3.0.3, OS required: Linux, MacOS
* Chapter number: 2 to 10, Software required: Python 3.4, OS required: Linux, MacOS, Unix
* Chapter number: 2 and 10, Software required: Node.js >= 0.12.7, OS required: Windows, MacOS, Linux/Unix
* Chapter number: 1 to 10, Software required: Redis >= 3.0.3, OS required: Linux, MacOS
* Chapter number: 2, Software required: Nohm >= 0.9.7, OS required: Windows, MacOS, Linux/Unix
* Chapter number: 2 to 10, Software required: Redis.py >= 2, OS required: Windows, MacOS, Linux/Unix
* Chapter number: 4, Software required: Git >= 1.9.4, OS required: Windows, MacOS, Linux/Unix
* Chapter number: 4 to 10, Software required: Linked Data Fragments Server >= 0.5.0 (https://github.com/jermnelson/linked-data-fragments/tree/development), OS required: Windows, MacOS, Linux/Unix
* Chapter number: 4 and 5, Software required: ToDo Project at https://github.com/amirrajan/nodejs-todo, OS required: Windows, MacOS, Linux/Unix
* Chapter number: 5, Software required: Lua >= 5.3.1, OS required: Windows, MacOS, Linux/Unix
* Chapter number: 6, Software required: Twemproxy >= 0.4.1, OS required: Linux/Unix/MacOS
* Chapter number: 7, Software required: MongoDB >= 3.0.5, OS, Elasticsearch >- 1.7.1, Fedora Commons > 4.3.0, R3 (http://heynemann.github.io/r3/) OS required: Windows, MacOS, Linux/Unix


## Check out Mapt to learn the most in-demand developer skills

[![](https://github.com/PacktPublishing/Mastering-Redis/blob/master/Images/mapt-pplogo.png)](https://www.packtpub.com/books/subscription/mapt)

### For more information on Mapt check our [![](https://github.com/PacktPublishing/Mastering-Redis/blob/master/Images/Youtube.PNG)] (https://www.youtube.com/watch?v=rljqYnpuQyg) video


## Redis books and videos

* [Learning Redis [Video]] (https://www.packtpub.com/big-data-and-business-intelligence/learning-redis-video?utm_source=github&utm_medium=repository&utm_campaign=9781784396596)
* [Redis Essentials] (https://www.packtpub.com/big-data-and-business-intelligence/redis-essentials?utm_source=github&utm_medium=repository&utm_campaign=9781784392451)
* [Building Databases with Redis [Video]] (https://www.packtpub.com/big-data-and-business-intelligence/building-databases-redis-video?utm_source=github&utm_medium=repository&utm_campaign=9781783284115)






### Download a free PDF

 <i>If you have already purchased a print or Kindle version of this book, you can get a DRM-free PDF version at no cost.<br>Simply click on the link to claim your free PDF.</i>
<p align="center"> <a href="https://packt.link/free-ebook/9781783988181">https://packt.link/free-ebook/9781783988181 </a> </p>
test edit 
