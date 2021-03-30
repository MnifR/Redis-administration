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
- [Keys](#keys)
- [Commands](#commands)
- [Bugs and feature requests](#bugs-and-feature-requests)

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
## Redis - Replication

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


## Redis - Sentinel(Fail-over)
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
## Redis - Backup

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
## Restore Redis Data

To restore Redis data, move Redis backup file (dump.rdb) into your Redis directory and start the server. To get your Redis directory, use CONFIG command of Redis as shown below.
```shell
127.0.0.1:6379> CONFIG get dir  
1) "data" 
2) "/home/user/backup"
```

## Restore Benchmark
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

## Contributing

Please read through our [contributing guidelines](https://reponame/blob/master/CONTRIBUTING.md). Included are directions for opening issues, coding standards, and notes on development.

Moreover, all HTML and CSS should conform to the [Code Guide](https://github.com/mdo/code-guide), maintained by [Main author](https://github.com/usernamemainauthor).

Editor preferences are available in the [editor config](https://reponame/blob/master/.editorconfig) for easy use in common text editors. Read more and download plugins at <https://editorconfig.org/>.

## Creators

**Creator 1**

- <https://github.com/usernamecreator1>

## Thanks

Some Text

## Copyright and license

Code and documentation copyright 2011-2018 the authors. Code released under the [MIT License](https://reponame/blob/master/LICENSE).

Enjoy :metal:
