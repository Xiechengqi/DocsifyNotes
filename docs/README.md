# Docsify 学习笔记

> **[官网中文手册](https://docsify.js.org/#zh-cn/)**

## 案例
> **https://docsify.js.org/#/zh-cn/awesome?id=showcase**
* https://Xiechengqi.github.io/DocsifyNotes
* https://Xiechengqi.github.io/AwesomeMacFork
* https://dunwu.github.io/frontend-tutorial
* https://docsify.js.org
* https://ripperhe.com/awesome-mac-html/#/
* https://jalan.space/leetcode-notebook

## 命令

``` shell
# 安装
npm i docsify-cli -g
# 初始化
docsify init [项目路径]
# 启动
docsify serve [项目路径]
# 或
python3 -m http.server
```

## 侧边栏

### 效果1
**[在线预览](https://Xiechengqi.github.io/DocsifyNotes/sidebardemo1)** - **[源码](https://github.com/Xiechengqi/DocsifyNotes/tree/master/docs/sidebardemo1)**

![SideBarDemo1](./sidebardemo1.gif)

### 效果2
**[在线预览](https://Xiechengqi.github.io/DocsifyNotes/sidebardemo2)** - **[源码](https://github.com/Xiechengqi/DocsifyNotes/tree/master/docs/sidebardemo2)**

![SideBarDemo2](./sidebardemo2.gif)

----

**https://github.com/hyperledger/blockchain-explorer/blob/main/Dockerfile**
<details><summary>展开</summary><pre><code>

``` yaml
# Copyright Tecnalia Research & Innovation (https://www.tecnalia.com)
# Copyright Tecnalia Blockchain LAB
#
# SPDX-License-Identifier: Apache-2.0

FROM node:13-alpine AS BUILD_IMAGE

# default values pf environment variables
# that are used inside container

ENV DEFAULT_WORKDIR /opt
ENV EXPLORER_APP_PATH $DEFAULT_WORKDIR/explorer

# set default working dir inside container
WORKDIR $EXPLORER_APP_PATH

COPY . .

# install required dependencies by NPM packages:
# current dependencies are: python, make, g++
RUN apk add --no-cache --virtual npm-deps python3 make g++ curl bash && \
    python3 -m ensurepip && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    rm -r /root/.cache

# install node-prune (https://github.com/tj/node-prune)
RUN curl -sfL https://install.goreleaser.com/github.com/tj/node-prune.sh | bash -s -- -b /usr/local/bin

# install NPM dependencies
RUN npm install && npm run build && npm prune --production

# build explorer app
RUN cd client && npm install && npm prune --production && yarn build

# remove installed packages to free space
RUN apk del npm-deps
RUN /usr/local/bin/node-prune

RUN rm -rf node_modules/rxjs/src/
RUN rm -rf node_modules/rxjs/bundles/
RUN rm -rf node_modules/rxjs/_esm5/
RUN rm -rf node_modules/rxjs/_esm2015/
RUN rm -rf node_modules/grpc/deps/grpc/third_party/

FROM node:13-alpine

# database configuration
ENV DATABASE_HOST 127.0.0.1
ENV DATABASE_PORT 5432
ENV DATABASE_NAME fabricexplorer
ENV DATABASE_USERNAME hppoc
ENV DATABASE_PASSWD password
ENV EXPLORER_APP_ROOT app

ENV DEFAULT_WORKDIR /opt
ENV EXPLORER_APP_PATH $DEFAULT_WORKDIR/explorer

WORKDIR $EXPLORER_APP_PATH

COPY . .
COPY --from=BUILD_IMAGE $EXPLORER_APP_PATH/dist ./app/
COPY --from=BUILD_IMAGE $EXPLORER_APP_PATH/client/build ./client/build/
COPY --from=BUILD_IMAGE $EXPLORER_APP_PATH/node_modules ./node_modules/

# expose default ports
EXPOSE 8080

# run blockchain explorer main app
CMD npm run app-start && tail -f /dev/null
```
</code></pre></details>

----
