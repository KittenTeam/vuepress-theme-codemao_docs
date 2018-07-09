# 编程猫文档中心使用说明

为了规范文档开发及部署方式及提高文档用户体验，特将编程猫对外文档统一编写维护。请在使用前务必阅读该文档

## 为什么要做这个？

参见[这篇 wiki](https://phab.srv.codemao.cn/w/kitten/docs_migrate/)

## 分支 

kitten_docs 分支对应新版 kitten 源码图鉴文档，未来更多文档将切换不同分支进行编写及部署

##  编写

* 1.拉取仓库

```shell
git clone ssh://git@phab.srv.codemao.cn/source/docs4child.git
```

* 2.切换需要编写的分支

```shell
// 例如切换kitten文档
git checkout kitten_docs
```

* 3.安装开发依赖

```shell
// 请在文档根目录运行命令
npm i --registry=https://registry.npm.taobao.org
```

* 4.开发模式

运行以下命令进入开发模式，默认使用`http://localhost:8080`查看文档编写效果，支持热刷新，如果修改配置文件，需要重启一次服务

```shell
// 请在文档根目录运行命令
npm run dev
```

* 5.部署

 使用 CI/CD 的方式部署，每次开发完毕将分支推送到远程对应分支则在 docker 进行相关文档构建及部署

## 编写注意事项
