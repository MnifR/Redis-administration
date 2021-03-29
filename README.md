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

- [Presentation](#presentation)
- [Install](#install)
- [Command](#command)
- [Bugs and feature requests](#bugs-and-feature-requests)

## Quick start

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

## Commands

Some text

```text
folder1/
└── folder2/
    ├── folder3/
    │   ├── file1
    │   └── file2
    └── folder4/
        ├── file3
        └── file4
```

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
