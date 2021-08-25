关键字：为什么线上问题 找不到问题呢
代码混淆了，源文件无法追踪
寻找问题的效率


安装：docker 和 docker-compose
执行 git clone https://github.com/getsentry/onpremise.git 克隆到主机
执行 docker volume create --name=sentry-data && docker volume create --name=sentry-postgres - 使用 docker volume 必须手动创建本地数据库和 sentry 卷
执行 docker-compose run --rm web config generate-secret-key - 生成密钥。将它添加到 .env 作为 SENTRY_SECRET_KEY的值，还要将其添加到 docker-compose.yml中

执行 docker-compose run --rm web createuser 创建用户。

Docker-Compose项目是Docker官方的开源项目，负责实现对Docker容器集群的快速编排。


1. 他是干什么的：错误捕获，便于开发人员快速定位，不止前端，后端也可以使用
2. 怎么样才能使用它:当搭建好sentry管理平台后，sentry会提供sdk用于内嵌到项目里
3. 前端项目中如何使用：
创建项目页面会自动跳转到如何配置vue项目页面。接下来就按照指引在vue代码里引入 sentry。可以通过 cdn 或者 npm 引入
```

import * as Sentry from '@sentry/browser';
import * as Integrations from '@sentry/integrations';
// 在生产环境中让sentry报错
process.env.NODE_ENV === "production" && Sentry.init({
  dsn: 'https://1111a5bc59b54778b75f4e3a92f2e462@sentry.io/1447145',
  integrations: [
    new Integrations.Vue({
      Vue,
      attachProps: true,
    }),
  ],
});
```
在项目跟目录下增加.sentryclirc文件，其中的token可以在左上角头像里的api keys里面获取


```
[auth]
token="your token"

[defaults]
url = https://sentry.io
org = "your org"
project = test
```
采用 webpack-plugin 这个插件，可以在 build 的同时自动上传 source-map

```
// webpack.prod.conf
const SentryCliPlugin = require("@sentry/webpack-plugin");
plugins:[
    new SentryCliPlugin({
        include: "./dist",
        release: process.env.RELEASE_VERSION,
        configFile: "sentry.properties"
    })
]


// main.js
Sentry.init({
    dsn: "https://4ec86726f2ba40338f66837c6b959eed@sentry.io/1447158",
    integrations: [
        new Integrations.Vue({
            Vue,
            attachProps: true
        }),
        new Integrations.RewriteFrames()
    ],
    release: process.env.RELEASE_VERSION
});


// prod.env.js
"use strict";
const release = "test-1";
process.env.RELEASE_VERSION = release;
module.exports = {
    NODE_ENV: '"production"',
    RELEASE_VERSION: `"${release}"`
};

需要保证 plugins 和 Sentry.init 两个配置中的 release 版本号相同，这样的话 sentry 才能将 source-map 文件一一对应上
```


4.钉钉配置啊 邮件配置啊

    
```
1.修改requirements.txt

cd onpremise
vi requirements.txt # 修改

添加 redis-py-cluster==1.3.4
```
    

```
2. 修改Dockerfile

vi Dockerfile
添加 RUN pip install git+https://github.com/L3T/sentry-dingding.git
```

```
3.重新执行 build

docker-composer build

docker-compose restart
```


```
写钉钉机器人的webhook
```



5. 如何捕获 如何发送一些自定义数据

```
主动捕获错误或者异常

try {
    aFunctionThatMightFail();
} catch (err) {
    Sentry.captureException(err);
}<br data-filtered="filtered">Sentry.captureException(new Error('test'))

另一种常见操作是捕获裸信息。消息只是应该发送给Sentry的一些文本信息。
Sentry.captureMessage('Something went wrong');
```

