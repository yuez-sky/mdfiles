## 服务器购买

## 服务器登陆

## 项目初始化
vue init webpack xxx

## 项目开发

### 前端移动组件库 vux
1. 安装vux
```
npm install vux -- save
```

2. 安装vux-loader
```
npm intall vux-loader --save-dev
```

3. 安装less-loader（正确编译less源码，否则会报'cannot GET'错误）
```
npm install less@2.7.3 less-loader --save-dev
```

4. 安装yaml-loader （以正确的语言文件读取）
```
npm install yaml-loader --save-dev
```

5. 在build/webpack.base.conf.js中配置

const vuxLoader = require("vux-loader");
```
const webpackConfig = originalConfig;  //将原来的module.exports赋值给变量webpackConfig

module.exports = vuxLoader.merge(webpackConfig,{
  plugins:['vux-ui']
});
```

### 后端
#### 数据库
docker 设置加速源
- /etc/docker/daemon.json
```
{
    "registry-mirrors": [
        "http://796958e9.m.daocloud.io"
    ],
    "insecure-registries": []
}
```

- 执行命令
```
docker run -p 3306:3306 -d --name todolist -e MYSQL_ROOT_PASSWORD=todopwd1234- mysql
```

#### api
