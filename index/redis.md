## Redis

- 为什么要用 redis/为什么要用缓存?

    高性能：假如用户第一次访问数据库中的某些数据。这个过程会比较慢，因为是从硬盘上读取的。将该用户访问的数据存在数缓存中，这样下一次再访问这些数据的时候就可以直接从缓存中获取了。操作缓存就是直接操作内存，所以速度相当快。并且由于局部性原理，某些数据被访问后极有可能被多次访问从而多次命中缓存。如果数据库中的对应数据改变的之后，同步改变缓存中相应的数据即可！

    高并发：直接操作缓存能够承受的请求是远远大于直接访问数据库的，所以我们可以考虑把数据库中的部分数据转移到缓存中去，这样用户的一部分请求会直接到缓存这里而不用经过数据库。缓存功能简单，说白了就是key-value式操作，单机支撑的并发量轻松一秒几万十几万，支撑高并发so easy。单机承载并发量是mysql单机的几十倍。

- 为什么要用 redis 而不用 map/guava 做缓存?

    缓存分为本地缓存和分布式缓存。以 Java 为例，使用自带的 map 或者 guava 实现的是本地缓存，最主要的特点是轻量以及快速，生命周期随着 jvm 的销毁而结束，并且在多实例的情况下，每个实例都需要各自保存一份缓存，缓存不具有一致性。

    使用 redis 或 memcached 之类的称为分布式缓存，在多实例的情况下，各实例共用一份缓存数据，缓存具有一致性。缺点是需要保持 redis 或 memcached服务的高可用，整个程序架构上较为复杂。

- redis 和 memcached 的区别

    1、Redis支持更丰富的数据类型（支持更复杂的应用场景）：Redis不仅仅支持简单的k/v类型的数据，同时还提供list，set，zset，hash等数据结构的存储。memcache支持简单的数据类型，String。
    2、Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用,而Memecache把数据全部存在内存之中。
    3、集群模式：memcached没有原生的集群模式，需要依靠客户端来实现往集群中分片写入数据；但是 redis 目前是原生支持 cluster 模式的.
    4、Memcached是多线程，非阻塞IO复用的网络模型；Redis使用单线程的多路 IO 复用模型。

    ![1](/images/redis-memcache.jpg)注：图中“单进程”应为“单线程”

- Redis为什么要搞成单线程呢？

    个人认为，有以下几个原因：

    1、redis有各种复杂的数据结构list, hash, set。也就是说，对于一个(key, value)，value的类型可以是list, hash, set。在实际应用场景中，很容易出现多个客户端对同一个key的这个复杂的value数据结构进行并发操作，如果是多线程，势必要引入锁，而锁却是性能杀手。相比较而言，memcached只有简单的get/set/add操作，没有复杂数据结构，在互斥这个问题上，没有redis那么严重。

    2、对于纯内存操作来说，cpu并不是瓶颈，瓶颈在网络IO上。所以即使单线程，也很快。另外，如果要利用多核的优势，可以在一个机器上开多个redis实例。

- 用了缓存之后会有啥不良的后果? 如何解决?

    1、缓存与数据库双写不一致

    读请求和写请求串行化，串到一个内存队列里去，这样就可以保证一定不会出现不一致的情况。

    一般来说，就是如果你的系统不是严格要求缓存+数据库必须一致性的话，缓存可以稍微的跟数据库偶尔有不一致的情况，最好不要做这个方案，串行化之后，就会导致系统的吞吐量会大幅度的降低，用比正常情况下多几倍的机器去支撑线上的一个请求。

    2、缓存雪崩

    事前：redis高可用，主从+哨兵，redis cluster，避免全盘崩溃

    事中：本地ehcache缓存 + hystrix限流&降级，避免MySQL被打死

    事后：redis持久化，快速恢复缓存数据

    3、缓存穿透

    每次系统从数据库中没有查到相应value，就写一个空值到缓存中去。

    4、缓存并发竞争

    可以基于zookeeper实现分布式锁，确保统一时间只能有一个系统实例在操作某个key，别人都不允许读写。每次要写之前先判断当前这个value的时间戳是否比缓存中的value的时间戳更新，如若更新则可以写。

    而且redis自己就有天然解决这个问题的CAS类的乐观锁方案

- 为啥 redis 单线程模型也能效率这么高?

    1、纯内存操作
    2、核心是基于非阻塞的IO多路复用机制
    3、单线程反而避免了多线程的频繁上下文切换问题

- 如何保证Redis的高并发和高可用？redis的主从复制原理能介绍一下么？redis的哨兵原理能介绍一下么?

    redis高并发：主从架构，一主多从，一般来说，很多项目其实就足够了，单主用来写入数据，单机几万QPS，多从用来查询数据，多个从实例可以提供每秒10万的QPS。

    redis高并发的同时，还需要容纳大量的数据：一主多从，每个实例都容纳了完整的数据，比如redis主就10G的内存量，其实你就最对只能容纳10g的数据量。如果你的缓存要容纳的数据量很大，达到了几十g，甚至几百g，或者是几t，那你就需要redis集群，而且用redis集群之后，可以提供可能每秒几十万的读写并发。

    redis高可用：如果你做主从架构部署，其实就是加上哨兵就可以了，就可以实现，任何一个实例宕机，自动会进行主备切换。

    全量复制：rdb生成、rdb通过网络拷贝、master缓冲区命令传输、slave旧数据清理及重新加载rdb、slave aof rewrite

    增量复制：如果全量复制过程中，master-slave网络连接断掉，那么salve重新连接master时，会触发增量复制。msater根据slave发送的psync中的offset来从backlog中获取数据，发送给slave node，默认backlog就是1MB。

    异步复制：master每次接收到写命令之后，先在内部写入数据，然后异步发送给slave node。

    哨兵：主备切换

- 解决异步复制和脑裂导致的数据丢失

    ```
    min-slaves-to-write 1
    min-slaves-max-lag 10
    ```

    要求至少有1个slave，数据复制和同步的延迟不能超过10秒

    如果说一旦所有的slave，数据复制和同步的延迟都超过了10秒钟，那么这个时候，master就不会再接收任何请求了

    上面两个配置可以减少异步复制和脑裂导致的数据丢失

    1、减少异步复制的数据丢失

    有了min-slaves-max-lag这个配置，就可以确保说，一旦slave复制数据和ack延时太长，就认为可能master宕机后损失的数据太多了，那么就拒绝写请求，这样可以把master宕机时由于部分数据未同步到slave导致的数据丢失降低的可控范围内

    2、减少脑裂的数据丢失

    如果一个master出现了脑裂，跟其他slave丢了连接，那么上面两个配置可以确保说，如果不能继续给指定数量的slave发送数据，而且slave超过10秒没有给自己ack消息，那么就直接拒绝客户端的写请求

    这样脑裂后的旧master就不会接受client的新数据，也就避免了数据丢失

    上面的配置就确保了，如果跟任何一个slave丢了连接，在10秒后发现没有slave给自己ack，那么就拒绝新的写请求

    因此在脑裂场景下，最多就丢失10秒的数据