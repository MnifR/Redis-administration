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
- [Data Types](#data-types)
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

## Data Types

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

Hashes

A Redis hash is a collection of key value pairs. Redis Hashes are maps between string fields and string values. Hence, they are used to represent objects.

```shell
redis 127.0.0.1:6379> HMSET user:1 username test password newtest points 200 
OK 
redis 127.0.0.1:6379> HGETALL user:1  
1) "username" 
2) "test" 
3) "password" 
4) "newtest" 
5) "points" 
6) "200"
```

Lists

Redis Lists are simply lists of strings, sorted by insertion order. You can add elements to a Redis List on the head or on the tail.

```shell
redis 127.0.0.1:6379> lpush test redis 
(integer) 1 
redis 127.0.0.1:6379> lpush test mongodb 
(integer) 2 
redis 127.0.0.1:6379> lpush test rabbitmq 
(integer) 3 
redis 127.0.0.1:6379> lrange test 0 10  

1) "rabbitmq" 
2) "mongodb" 
3) "redis"

```

Sets

Redis Sets are an unordered collection of strings. In Redis, you can add, remove, and test for the existence of members in O(1) time complexity.

```shell
redis 127.0.0.1:6379> sadd test redis 
(integer) 1 
redis 127.0.0.1:6379> sadd test mongodb 
(integer) 1 
redis 127.0.0.1:6379> sadd test rabbitmq 
(integer) 1 
redis 127.0.0.1:6379> sadd test rabbitmq 
(integer) 0 
redis 127.0.0.1:6379> smembers test  

1) "rabbitmq" 
2) "mongodb" 
3) "redis" 
```
Sorted Sets

Redis Sorted Sets are similar to Redis Sets, non-repeating collections of Strings. The difference is, every member of a Sorted Set is associated with a score, that is used in order to take the sorted set ordered, from the smallest to the greatest score. While members are unique, the scores may be repeated.

```shell
redis 127.0.0.1:6379> zadd test 0 redis 
(integer) 1 
redis 127.0.0.1:6379> zadd test 0 mongodb 
(integer) 1 
redis 127.0.0.1:6379> zadd test 0 rabitmq 
(integer) 1 
redis 127.0.0.1:6379> zadd test 0 rabitmq 
(integer) 0 
redis 127.0.0.1:6379> ZRANGEBYSCORE test 0 1000  

1) "redis" 
2) "mongodb" 
3) "rabbitmq" 
```
```shell
redis 127.0.0.1:6379> INFO
```

## Keys

Redis Keys Commands

Following table lists some basic commands related to keys.
Sr.No 	Command &                           Description
1 	    DEL key                             This command deletes the key, if it exists.
2 	    DUMP key                            This command returns a serialized version of the value stored at the specified key.
3 	    EXISTS key                          This command checks whether the key exists or not.
4 	    EXPIRE key seconds                  Sets the expiry of the key after the specified time.
5 	    EXPIREAT key timestamp              Sets the expiry of the key after the specified time. Here time is in Unix timestamp format.
6 	    PEXPIRE key milliseconds            Set the expiry of key in milliseconds.
7 	    PEXPIREAT key milliseconds-timestamp Sets the expiry of the key in Unix timestamp specified as milliseconds.
8 	    KEYS pattern                        Finds all keys matching the specified pattern.
9 	    MOVE key db                         Moves a key to another database.
10 	    PERSIST key                         Removes the expiration from the key.
11 	    PTTL key                            Gets the remaining time in keys expiry in milliseconds.
12     	TTL key                             Gets the remaining time in keys expiry.
13 	    RANDOMKEY                           Returns a random key from Redis.
14 	    RENAME key newkey                   Changes the key name.
15 	    RENAMENX key newkey                 Renames the key, if a new key doesn't exist.
16     	TYPE key                            Returns the data type of the value stored in the key.

## Commands

Redis Strings Commands

Following table lists some basic commands to manage strings in Redis.
Sr.No 	                                         Command & Description
1 	SET key value                                This command sets the value at the specified key.
2 	GET key                                      Gets the value of a key.
3 	GETRANGE key start end                       Gets a substring of the string stored at a key.
4 	GETSET key value

Sets the string value of a key and return its old value.
5 	GETBIT key offset

Returns the bit value at the offset in the string value stored at the key.
6 	MGET key1 [key2..]

Gets the values of all the given keys
7 	SETBIT key offset value

Sets or clears the bit at the offset in the string value stored at the key
8 	SETEX key seconds value

Sets the value with the expiry of a key
9 	SETNX key value

Sets the value of a key, only if the key does not exist
10 	SETRANGE key offset value

Overwrites the part of a string at the key starting at the specified offset
11 	STRLEN key

Gets the length of the value stored in a key
12 	MSET key value [key value ...]

Sets multiple keys to multiple values
13 	MSETNX key value [key value ...]

Sets multiple keys to multiple values, only if none of the keys exist
14 	PSETEX key milliseconds value

Sets the value and expiration in milliseconds of a key
15 	INCR key

Increments the integer value of a key by one
16 	INCRBY key increment

Increments the integer value of a key by the given amount
17 	INCRBYFLOAT key increment

Increments the float value of a key by the given amount
18 	DECR key

Decrements the integer value of a key by one
19 	DECRBY key decrement

Decrements the integer value of a key by the given number
20 	APPEND key value

Appends a value to a key

## Bugs and feature requests

Have a bug or a feature request? Please first read the [issue guidelines](https://reponame/blob/master/CONTRIBUTING.md) and search for existing and closed issues. If your problem or idea is not addressed yet, [please open a new issue](https://reponame/issues/new).

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
