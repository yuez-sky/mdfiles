## 目的
实现前端项目多个版本共存。如果只存在一份资源，会导致每次修改都会影响到所有的用户，对于持续更新的Web项目来说，需要保证每次的修改只会影响到小部分用户，而不是所有的，控制范围，减少损失。

## 实现
项目采用的是`git`版本控制

原直接以**master**分支作为production，功能修改完善合并到master上，然后直接在master上编辑提交，线上更新代码。

现在修改成：通过分支来控制，并保留多份代码，保留以后老版本修改的可能性

- 从master生成分支release-1.0.0
- releaes-1.0.0 在线上部署，通过指定路径来访问改目录的编译后文件
- 当有其他新功能时，产生新的分支release-x.x.x，部署访问

最后达到test.com/v1/，test.com/v2 这种访问效果。

## 项目配置的修改

- config/index.js
 
```
// webpack 编译的配置文件，
// 主要修改时动态改变编译后的文件放置目录，配合Apache控制访问

// 增加版本获取函数 getVersion()，通过匹配当前版本名称，来获取具体的版本号
// 要求，对版本号的命名有格式的要求
var shell = require('shelljs')
var os = require('os')
function getVersion() {
  function exec (cmd) {
    return shell.exec(cmd, {silent: true}).toString().trim()
  }
  var releaseName = exec("git branch -vv|grep '*'")
  if (os.platform === 'win32') {
    releaseName = exec("git branch -vv|findstr '*'")
  }
  var reg = /\[origin\/release\-v[\d.?]*\]/
  if (reg.test(releaseName)) {
    var arr = releaseName.match(reg)
    return arr[0].replace('[origin/release-', '').replace(/\./g, '').replace(']', '').toString()
  }
}

// 修改env配置 和 build下的assetsPublicPath
module.exports = {
  build: {
    env: {...require('./prod.env'), cashierVersion: "'" + getVersion() + "'"},
    index: path.resolve(__dirname, '../dist/index.html'),
    assetsRoot: path.resolve(__dirname, '../dist/'),
    assetsSubDirectory: 'static',
    assetsPublicPath: '/' + getVersion() + '/',
  },
  dev: {
    env: {...require('./dev.env'), cashierVersion: "'" + getVersion() + "'"},
  }
}

```

- src/router/index.js
```
// 修改route数组，增加根path路径
mode: 'history',
routes: [
    {
      path: process.env.NODE_ENV === 'development' ? '/' : ('/' + process.env.cashierVersion + '/'),
      component: xxx,
      children: [
          ...
      ]
    }
]
```



## 上线时Apache配置
配置Apache

当访问test.com/xxx，访问的是实际服务器部署的test目录下xxx/dist/index.html文件


## 访问多个版本
原本服务器上之后一个test目录，所有的文件都在test目录下，包括编译后的文件

现在服务器的test目录下有多个文件，如果vxx1， vxx2，根据实际的路由情况，访问到具体的目录下，如果有必要
- 可以以ip 或cookie 通过nginx来控制访问
- 可以通过登陆后的信息，在实际的项目代码中来控制
```
// vue-router 中有beforeEach
router.beforeEach((to, from, next) => {
    // 通过代码进行实际的访问控制
})

```