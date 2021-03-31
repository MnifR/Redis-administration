<p align="center">
  <a href="https://example.com/">
    <img src="https://banner2.cleanpng.com/20180803/zth/kisspng-redis-logo-database-mongodb-gearman-redis-slide-nosql-taiwan-6-clasense4-blog-5b6409cda154a2.0682164215332827656608.jpg" alt="Logo" width=450 height=150>
  </a>

  <h3 align="center">Logo</h3>

  <p align="center">
    Short description
    <br>
    <a href="https://reponame/issues/new?template=bug.md">Report bug</a>
    ·
    <a href="https://reponame/issues/new?template=feature.md&labels=feature">Request feature</a>
  </p>
</p>


## Table of contents

- [Start](#start)
- [Install](#install)
- [Configuration](#configuration)
- [Replication](#Replication)
- [Sentinel](#Sentinel)
- [Backup](#Backup)
- [Restore](#Restore)
- [Benchmark](#Benchmark)
- [Client Connection](#Client Connection)
- [Pipelining](#Pipelining)
- [Partitioning](#Partitioning)
- [Restore](#Restore)

## Start

Redis is an in-memory key-value store that can be used as a database, cache, and message broker. The project is open source and it's currently licensed under the BSD license.

Redis delivers sub-millisecond response times that enable millions of requests per second to power demanding real-time applications such as games, ad brokers, financial dashboards, and many more!

Redis is written in C.

## Install

Step 1 — Installing and Configuring Redis on Ubuntu 20.04

```shell

sudo apt update

sudo apt install redis-server

sudo nano /etc/redis/redis.conf
```
Inside the file, find the supervised directive.
This directive allows you to declare an init system to manage Redis as a service, providing you with more control over its operation
```shell

. . .

# If you run Redis from upstart or systemd, Redis can interact with your
# supervision tree. Options:
#   supervised no      - no supervision interaction
#   supervised upstart - signal upstart by putting Redis into SIGSTOP mode
#   supervised systemd - signal systemd by writing READY=1 to $NOTIFY_SOCKET
#   supervised auto    - detect upstart or systemd method based on
#                        UPSTART_JOB or NOTIFY_SOCKET environment variables
# Note: these supervision methods only signal "process is ready."
#       They do not enable continuous liveness pings back to your supervisor.
supervised systemd

. . .
```

```shell
sudo systemctl restart redis.service

sudo systemctl status redis

sudo netstat -lnp | grep redis


```
Start redis cli
```shell
redis-cli

127.0.0.1:6379> ping

```

Output
```shell
PONG
```

Add password to redis

```shell
sudo nano /etc/redis/redis.conf
```

Scroll to the SECURITY section and look for a commented directive that reads:
```shell

# requirepass very-strong-password

```

Generate a very strong password

```shell
openssl rand 60 | openssl base64 -A
```

After setting the password, save and close the file, then restart Redis:
```shell
sudo systemctl restart redis.service

```
```shell
redis-cli

127.0.0.1:6379> 
auth very-strong-password

```

Yoou can set the password in the config file.

```shell
127.0.0.1:6379> CONFIG get requirepass 
1) "requirepass" 
2) "" 
```

By default, this property is blank, which means no password is set for this instance. You can change this property by executing the following command.
```shell
127.0.0.1:6379> CONFIG set requirepass "tutorialspoint" 
OK 
127.0.0.1:6379> CONFIG get requirepass 
1) "requirepass" 
2) "very-strong-password"
```

Renaming Dangerous Commands

Some of the commands that are considered dangerous include: FLUSHDB, FLUSHALL, KEYS, PEXPIRE, DEL, CONFIG, SHUTDOWN, BGREWRITEAOF, BGSAVE, SAVE, SPOP, SREM, RENAME, and DEBUG. This is not a comprehensive list, but renaming or disabling all of the commands in that list is a good starting point for enhancing your Redis server’s security.

```shell
sudo nano /etc/redis/redis.conf
```

```shell

...
# It is also possible to completely kill a command by renaming it into
# an empty string:
#
rename-command FLUSHDB ""
rename-command FLUSHALL ""
rename-command DEBUG ""
...
```

## Configuration

Following is the basic syntax of Redis CONFIG command.

```shell
redis 127.0.0.1:6379> CONFIG GET CONFIG_SETTING_NAME
```
```shell
redis 127.0.0.1:6379> CONFIG GET loglevel  
1) "loglevel" 
2) "notice"
```
```shell
redis 127.0.0.1:6379> CONFIG GET *
```
SET and GET are Redis commands, name is the key used in Redis and test is the string value that is stored in Redis.

Note − A string value can be at max 512 megabytes in length.

```shell
redis 127.0.0.1:6379> SET name "test" 
OK 
redis 127.0.0.1:6379> GET name 
"test"
```

Run Commands on the Remote Server
```shell
$ redis-cli -h host -p port -a password
```

Following example explains how we can get all statistics and information about the server.
```shell
redis 127.0.0.1:6379> INFO 
# Server
redis_version:6.0.12
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:cbd22e18adfcd606
redis_mode:standalone
os:Linux 5.4.0-1041-aws x86_64
arch_bits:64
multiplexing_api:epoll
atomicvar_api:atomic-builtin
gcc_version:10.2.1
process_id:1
run_id:f938cbb50e87a561a14874b833b7b4e1c1859bbe
tcp_port:6379
uptime_in_seconds:333773
uptime_in_days:3
hz:10
configured_hz:10
lru_clock:6495628
executable:/data/redis-server
config_file:/etc/redis/redis.conf
io_threads_active:0
...

```
## Replication

redis.conf
```shell
#persistence
dir /data
dbfilename dump.rdb
appendonly yes
appendfilename "appendonly.aof"
```
Redis-1 configuration
```shell
protected-mode no
port 6379

#authentication
masterauth very-strong-password
requirepass very-strong-password
```

Redis-2 configuration
```shell
protected-mode no
port 6379
slaveof redis-1 6379

#authentication
masterauth very-strong-password
requirepass very-strong-password
```
Redis-3 configuration
```shell
protected-mode no
port 6379
slaveof redis-1 6379

#authentication
masterauth very-strong-password
requirepass very-strong-password
```
[Replication guidelines](https://redis.io/topics/replication)


## Sentinel
Redis configuration Sentinel
sentinel.conf
```shell
#********BASIC CONFIG************************************
port 5000
sentinel monitor mymaster redis-1 6379 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 60000
sentinel parallel-syncs mymaster 1
sentinel auth-pass mymaster a-very-complex-password-here
#********************************************

```
[Sentinel guidelines](https://redis.io/topics/sentinel) 
## Backup

Redis SAVE command is used to create a backup of the current Redis database.
```shell
127.0.0.1:6379> SAVE  
OK 
```
This command will create a dump.rdb and appendonly.aof  file in your Redis directory.

Bgsave

To create Redis backup, an alternate command BGSAVE is also available. This command will start the backup process and run this in the background.
```shell
127.0.0.1:6379> BGSAVE  
Background saving started
```
## Restore

To restore Redis data, move Redis backup file (dump.rdb) into your Redis directory and start the server. To get your Redis directory, use CONFIG command of Redis as shown below.
```shell
127.0.0.1:6379> CONFIG get dir  
1) "data" 
2) "/home/user/backup"
```

## Benchmark
Redis benchmark is the utility to check the performance of Redis by running n commands simultaneously.
```shell
redis-benchmark [option] [option value]

redis-benchmark -n 100000
PING_INLINE: 141043.72 requests per second 
PING_BULK: 142857.14 requests per second 
SET: 141442.72 requests per second 
GET: 145348.83 requests per second 
INCR: 137362.64 requests per second 
LPUSH: 145348.83 requests per second 
LPOP: 146198.83 requests per second 
SADD: 146198.83 requests per second 
SPOP: 149253.73 requests per second 
LPUSH (needed to benchmark LRANGE): 148588.42 requests per second 
LRANGE_100 (first 100 elements): 58411.21 requests per second 
LRANGE_300 (first 300 elements): 21195.42 requests per second 
LRANGE_500 (first 450 elements): 14539.11 requests per second 
LRANGE_600 (first 600 elements): 10504.20 requests per second 
MSET (10 keys): 93283.58 requests per second

```

Following is a list of available options in Redis benchmark.

| No | Option | Description                                              | Default Value |
|----|--------|----------------------------------------------------------|---------------|
| 1  | -h     | Specifies server host name                               | 127.0.0.1     |
| 2  | -p     | Specifies server port                                    | 6379          |
| 3  | -s     | Specifies server socket                                  |               |
| 4  | -c     | Specifies the number of parallel connections             | 50            |
| 5  | -n     | Specifies the total number of requests                   | 10000         |
| 6  | -d     | Specifies data size of SET/GET value in bytes            | 2             |
| 7  | -k     | 1=keep alive, 0=reconnect                                | 1             |
| 8  | -r     | Use random keys for SET/GET/INCR, random values for SADD |               |
| 9  | -p     | Pipeline <numreq> requests                               | 1             |
| 10 | -h     | Specifies server host name                               |               |
| 11 | -f     | Forces Quiet to Redis. Just shows query/sec values       |               |
| 12 | -csv   | Output in CSV format                                     |               |
| 13 | -l     | Generates loop, Run the tests forever                    |               |
| 14 | -t     | Only runs the comma-separated list of tests              |               |
| 15 | -I     | Idle mode. Just opens N idle connections and wait        |               |
  
Following example shows the multiple usage options in Redis benchmark utility.

```shell
redis-benchmark -h 127.0.0.1 -p 6379 -t set,lpush -n 100000 -q  

SET: 146198.83 requests per second 
LPUSH: 145560.41 requests per second 
```

## Client Connection
Maximum Number of Clients

Redis accepts clients’ connections on the configured listening TCP port and on the Unix socket, if enabled. When a new client connection is accepted, the following operations are performed −

    The client socket is put in non-blocking state since Redis uses multiplexing and non-blocking I/O.

    The TCP_NODELAY option is set in order to ensure that we don't have delays in our connection.

    A readable file event is created so that Redis is able to collect the client queries as soon as new data is available to be read on the socket.

Maximum Number of Clients

In Redis config (redis.conf), there is a property called maxclients, which describes the maximum number of clients that can connect to Redis.

Following is the basic syntax of command.


```shell
config get maxclients  

1) "maxclients" 
2) "10000" 
```

By default, this property is set to 10000 (depending upon the maximum number of file descriptors limit of OS), although you can change this property.
Example

In the following example, we have set the maximum number of clients to 100000, while starting the server.


```shell
redis-server --maxclients 100000 
```

| No | Command        | Description                                                                                                                    |
|----|----------------|--------------------------------------------------------------------------------------------------------------------------------|
| 1  | CLIENT LIST    | Returns the list of clients connected to Redis server                                                                          |
| 2  | CLIENT SETNAME | Assigns a name to the current connection                                                                                       |
| 3  | CLIENT GETNAME | Returns the name of the current connection as set by CLIENT SETNAME                                                            |
| 4  | CLIENT PAUSE   | This is a connections control command able to suspend all the Redis clients for the specified amount of time (in milliseconds) |
| 5  | CLIENT KILL    | This command closes a given client connection.                                                                                 |

## Pipelining

Redis is a TCP server and supports request/response protocol. In Redis, a request is accomplished with the following steps −

    The client sends a query to the server, and reads from the socket, usually in a blocking way, for the server response.

    The server processes the command and sends the response back to the client.

Meaning of Pipelining

The basic meaning of pipelining is, the client can send multiple requests to the server without waiting for the replies at all, and finally reads the replies in a single step.
Example


To check the Redis pipelining, just start the Redis instance and type the following command in the terminal.
```shell
$(echo -en "PING\r\n SET test redis\r\nGET test\r\nINCR visitor\r\nINCR visitor\r\nINCR visitor\r\n"; sleep 10) | nc localhost 6379  
+PONG 
+OK 
redis 
:1 
:2 
:3 
```

In the above example, we will check Redis connection by using PING command. We have set a string named tutorial with value redis. Later, we get that keys value and increment the visitor number three times. In the result, we can see that all commands are submitted to Redis once, and Redis provides the output of all commands in a single step.
Benefits of Pipelining

The benefit of this technique is a drastically improved protocol performance. The speedup gained by pipelining ranges from a factor of five for connections to localhost up to a factor of at least one hundred over slower internet connections.

## Partitioning

Partitioning is the process of splitting your data into multiple Redis instances, so that every instance will only contain a subset of your keys.
Benefits of Partitioning

    It allows for much larger databases, using the sum of the memory of many computers. Without partitioning you are limited to the amount of memory that a single computer can support.

    It allows to scale the computational power to multiple cores and multiple computers, and the network bandwidth to multiple computers and network adapters.

Disadvantages of Partitioning

    Operations involving multiple keys are usually not supported. For instance, you can't perform the intersection between two sets if they are stored in the keys that are mapped to different Redis instances.

    Redis transactions involving multiple keys cannot be used.

    The partitioning granuliary is the key, so it is not possible to shard a dataset with a single huge key like a very big sorted set.

    When partitioning is used, data handling is more complex. For instance, you have to handle multiple RDB/AOF files, and to get a backup of your data you need to aggregate the persistence files from multiple instances and hosts.

    Adding and removing the capacity can be complex. For instance, Redis Cluster supports mostly transparent rebalancing of data with the ability to add and remove nodes at runtime. However, other systems like client-side partitioning and proxies don't support this feature. A technique called Presharding helps in this regard.

Types of Partitioning

There are two types of partitioning available in Redis. Suppose we have four Redis instances, R0, R1, R2, R3 and many keys representing users like user:1, user:2, ... and so forth.
Range Partitioning

Range partitioning is accomplished by mapping ranges of objects into specific Redis instances. Suppose in our example, the users from ID 0 to ID 10000 will go into instance R0, while the users from ID 10001 to ID 20000 will go into instance R1 and so forth.
Hash Partitioning

In this type of partitioning, a hash function (eg. modulus function) is used to convert the key into a number and then the data is stored in different-different Redis instances.



