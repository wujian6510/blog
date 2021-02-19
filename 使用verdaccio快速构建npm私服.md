# 使用verdaccio
 verdaccio是基于node服务开发的轻量私有级别的npm代理服务器，因此安装verdaccio之前必须先保证安装好了node
 
##  安装verdaccio
 使用npm全局安装,mac需要注意sudo
```
npm install verdaccio
```
 安装完成，检测,如果能正常显示帮助信息则表明安装成功
 
```
verdaccio -h

*** WARNING: Verdaccio doesn't need superuser privileges. Don't run it under root! ***
Usage: verdaccio [options]

Options:
  -i, --info                  prints debugging information about the local environment
  -l, --listen <[host:]port>  host:port number to listen on (default: localhost:4873)
  -c, --config <config.yaml>  use this configuration file (default: ./config.yaml)
  -V, --version               output the version number
  -h, --help                  output usage information
```

## 运行verdaccio
运行verdaccio非常简单，直接输入verdaccio命令即可

```
verdaccio

*** WARNING: Verdaccio doesn't need superuser privileges. Don't run it under root! ***
 warn --- config file  - /root/.config/verdaccio/config.yaml
 warn --- Verdaccio started
 warn --- Plugin successfully loaded: verdaccio-htpasswd
 warn --- Plugin successfully loaded: verdaccio-audit
 warn --- http address - http://0.0.0.0:4873/ - verdaccio/4.10.0
```
这是就可以直接访问 http://0.0.0.0:4873/
我的服务地址为：[http://10.45.47.161:4873/]( http://10.45.47.161:4873/)

## 使用pm2来启动verdaccio

使用verdaccio启动的服务 ，如果终端关掉了，服务也就停止了，如果需要服务一直在后台跑着，可以借助与pm2工具来启动

安装pm2

```
npm install pm2 -g 
```
启动verdaccio

```
pm2 start verdaccio
```
查看pm2运行结果

```
pm2 list  
┌─────┬──────────────┬─────────────┬─────────┬─────────┬──────────┬───────────┬──────────┐
│ id  │ name         │ namespace   │ version │ mode    │ pid      │ uptime │ ↺    │ status    │ cpu      │ mem      │ user     │ watching │
├─────┼──────────────┼─────────────┼─────────┼─────────┼──────────┼────┼──────┼──────────┤
│ 0   │ verdaccio    │ default     │ N/A     │ fork    │ 16791    │ 41D    │ 0    │ online    │ 0%       │ 231.5mb  │ root     │ disabled │
└─────┴──────────────┴─────────────┴─────────┴─────────┴──────────┴───────────┴──────────┘
```

## 相关的配置
从启动成功的消息提示来看，配置文件在 /root/.config/verdaccio/config.yaml
使用vim命令直接打开，默认的配置如下：


```
#
# This is the default config file. It allows all users to do anything,
# so don't use it on production systems.
#
# Look here for more config file examples:
# https://github.com/verdaccio/verdaccio/tree/master/conf
#

# path to a directory with all packages
storage: ./storage
# path to a directory with plugins to include
plugins: ./plugins

web:
  title: Verdaccio
  # comment out to disable gravatar support
  # gravatar: false
  # by default packages are ordercer ascendant (asc|desc)
  # sort_packages: asc
  # convert your UI to the dark side
  # darkMode: true

# translate your registry, api i18n not available yet
# i18n:
# list of the available translations https://github.com/verdaccio/ui/tree/master/i18n/translations
#   web: en-US

auth:
  htpasswd:
    file: ./htpasswd
    # Maximum amount of users allowed to register, defaults to "+inf".
    # You can set this to -1 to disable registration.
    # max_users: 1000

# a list of other known repositories we can talk to
uplinks:
  npmjs:
    url: https://registry.npmjs.org/
    agent_options:
      keepAlive: true
      maxSockets: 40
      maxFreeSockets: 10

packages:
  '@ued/*':
    # scoped packages
    access: $all
    publish: $authenticated
    unpublish: $authenticated
    proxy: npmjs

  '**':
    # allow all users (including non-authenticated users) to read and
    # publish all packages
    #
    # you can specify usernames/groupnames (depending on your auth plugin)
    # and three keywords: "$all", "$anonymous", "$authenticated"
    access: $all
     # allow all known users to publish/publish packages
    # (anyone can register by default, remember?)
    publish: $authenticated
    unpublish: $authenticated

    # if package is not available locally, proxy requests to 'npmjs' registry
    proxy: npmjs

# You can specify HTTP/1.1 server keep alive timeout in seconds for incoming connections.
# A value of 0 makes the http server behave similarly to Node.js versions prior to 8.0.0, which did not have a keep-alive timeout.
# WORKAROUND: Through given configuration you can workaround following issue https://github.com/verdaccio/verdaccio/issues/301. Set to 0 in case 60 is not enough.
server:
  keepAliveTimeout: 60

middlewares:
  audit:
    enabled: true

# log settings
listen: 0.0.0.0:4873
logs:
  - { type: stdout, format: pretty, level: http }
  #- {type: file, path: verdaccio.log, level: info}
#experiments:
#  # support for npm token command
#  token: false
#  # support for the new v1 search endpoint, functional by incomplete read more on ticket 1732
#  search: false
#  # disable writing body size to logs, read more on ticket 1912
#  bytesin_off: false

# This affect the web and api (not developed yet)
#i18n:
#web: en-US
```
- uplinks: 第三方依赖源配置
- package: 包管理，可以配置匹配的包名去哪里下载
- listen: 启动的端口，默认 4873

# 本地使用

## 优雅设置npm源
npm私服搭建完成了，本地就可以使用这个源了，
由于本地使用的npm源比较多的，可以使用nrm来管理，来回切换
### 安装nrm

```
npm install nrm -g
```

### 查看npm源列表 nrm ls

```
WUJIAN:~ wujian$ nrm ls

  npm -------- https://registry.npmjs.org/
  yarn ------- https://registry.yarnpkg.com/
  cnpm ------- http://r.cnpmjs.org/
  taobao ----- https://registry.npm.taobao.org/
  nj --------- https://registry.nodejitsu.com/
  npmMirror -- https://skimdb.npmjs.com/registry/
  edunpm ----- http://registry.enpmjs.org/
```

### 增加一个源 nrm add

gzued为别名，后面跟着具体的路径
```
nrm add gzued http://10.45.47.161:4873/

```
### 切换源 nrm use 

```
nrm use gzued
```

## 发布包
### 增加用户
使用npm addUser 增加用户 
依次输入用户名、密码等信息

```
npm addUser wujian
```
### 发布包
发布的npm包需要遵循一些规范，这个可以参考: npm包发布指南

然后在当前目录下使用 npm publish即可

注意：发布之前需要用命令 nrm ls 确定一下当前使用的源为需要发布的源


```
 npm publish
```
