# Vue 服务端渲染（Server Siding Render）


## 对比
优点
- 利于SEO
- 更快的内容到达时间

缺点
- 开发条件限制
    - 浏览器特定的代码只能在**特定的函数钩子**中使用
    - 某些**外部拓展库**可能需要**特殊的处理**才能使用
- Nodejs的服务器环境
- 服务器负载

SSR是否使用**取决于内容到达时间(time-to-content)的重要性**。

如果只是想改善部分页面的**SEO**，可以采用**预渲染**
- 不需要web服务器实时动态编译Html
- 在构建时根据路由生成特定的静态Html文件
- 使用起来更简单

## 预渲染（Prerendering）
Vue预渲染插件`Prerender-spa-plugin`

[GitHub地址](https://github.com/chrisvfritz/prerender-spa-plugin)

### 解决
- SEO
- Slow Client
- OpenGraph/Social Metadata

### 不能解决
- User-specific content、
    - 不同的用户看都会不同
- Frequently changing Content
    - 会展示旧的内容
- Thousands of routers
    - 构建慢


### 实现
主要通过`Webpack`插件的方式，控制编译出来的静态文件的方式。

Webpack简单配置
```
// webpack.conf.js
var path = require('path')
var PrerenderSpaPlugin = require('prerender-spa-plugin')
 
module.exports = {
  // ...
  plugins: [
    new PrerenderSpaPlugin(
      // Absolute path to compiled SPA
      path.join(__dirname, '../dist'),
      // List of routes to prerender
      [ '/', '/about', '/contact' ]
    )
  ]
}
```

- 打包完成后，你会发现原来的`dist`目录下，多出来`about`,`contact`这样的目录，这个目录下会有对应的`index.html`文件；这份文件是对应路由生产后的静态页面，利用SEO

- 通过配置`Apache`或`Nginx`等访问，匹配相应的路由访问导指定目录下index.html文件，即可。



遇到的几个问题：
    - 项目中使用的版本为2.1.0版本，@next版本的没有安装成功，有个chromium driver下载不了，会导致后续在build的过程中会报错
    - 如果没有对应路由或者拼写错误的话，build也会报错
    - webpack中配置的route需要与router一直，注意`/`符号

## Vue 服务端渲染


## 服务端渲染框架 NUXT.js