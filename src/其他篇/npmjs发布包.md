## 如何在 npm 上发布自己的包

### 一、注册账号

​ 登录https://www.npmjs.com/注册账号

### 二、初始化项目

​ 进入项目目录

```javascript
	cd myproject

	npm init

	package name: (新建文件夹) my-page ---- 包名
    version: (1.0.0)                 ---- 版本号
    description:                     ---- 描述
    entry point: (index.js)          ---- 入口文件
    test command:                    ---- 测试命令
    git repository:					 ---- git地址
    keywords: page					 ---- 关键字，可以通过关键字在npmjs.com上搜索
    author: jianyuoYang 			 ---- 作者
    license: (ISC)

```

Tips：如果你的包引用了第三方包，则需要在 package.json 文件种增加 dependencies 节点，写入依赖的包及版本

```
  "dependencies": {
    "colors": "^1.3.2",
    "on-finished": "^2.3.0"
  }
```

### 三、账号登录

使用

```
npm login
```

登录账号

Tips：如果 npm 切换到淘宝镜像，需要把镜像切回 npm，否则无法登录

这里分享一个好用的 npm 镜像管理工具**nrm**

先全局安装

```javascript
npm install nrm -g
```

然后就可以使用 nrm 命令切换镜像源

常用命令：

```javascript
nrm test				----会返回每个镜像源的连接速度及当前使用的镜像源

输出结果如下：
* npm ---- 830ms
  yarn --- 912ms
  cnpm --- 752ms
  taobao - 187ms
  nj ----- Fetch Error
  npmMirror  2965ms
  edunpm - 845ms


nrm ls					----会返回可使用的全部镜像源

输出结果如下：
* npm -------- https://registry.npmjs.org/
  yarn ------- https://registry.yarnpkg.com/
  cnpm ------- http://r.cnpmjs.org/
  taobao ----- https://registry.npm.taobao.org/
  nj --------- https://registry.nodejitsu.com/
  npmMirror -- https://skimdb.npmjs.com/registry/
  edunpm ----- http://registry.enpmjs.org/

nrm use npm				---	切换至对应镜像源

使用方法如下：
 nrm use npm


   Registry has been set to: https://registry.npmjs.org/
```

### 四、发布包

登录后可直接输入命令

```
npm publish
```

发布自己的包,如果有报错，则查看报名是否和已有包名重复，更改包名后重新发布即可。

如果要撤回自己所发布的包某个版本，则使用

```
npm unpublish 包名@版本号
```

命令即可撤回对应版本的包

若要删除整个包使用

```
npm unpublish 包名 --force
```

命令撤回整个已发布包
