# Nodejs + Face++ 识别身份证照片

## 起因
因为有这样一件事情，需要获取身份证正面照中的姓名和身份证号，如果全部通过眼看手敲的话，很费事，而且浪费时间，作为一个程序员，当然能想如果能自动化执行就好了。

## 实现
### 图像识别Api
图片识别暂时还没有接触过，所以就去找了能够提供图像识别的第三方应用，这边我是用的是**Face++**
- 登陆注册并完善相关信息后，提供了`AppKey`和`AppSecret`来调用提供的Api
- 在使用`Postman`简单测试后，发现可行，post请求后，提供的接口会返回一组身份的信息JSON数据

> Postman 是通过能强大的网页调试与发送网页HTTP请求，并能运行测试用例的的Chrome插件，现在提供了客户端。只要在GUI界面里，填写相应的URl和数据，选择相应的方法，`POST`、 `GET`、 `PUT`、 `DELETE`，就能够将结果返回回来，不需要编写代码。[官网链接](https://www.getpostman.com/)


### Nodejs实现
我是在mac的终端下开发的，如果是window环境系，部分命令不适用，需要替换合适的命令。

1. 目录结构


```
/**
 * images 资源文件
 * package.json  执行npm init 后会自动生产
 * index.js 实现的逻辑文件
 */

- idCard
    - index.js
    - images
        - ***.jpg
        - ***.png
        - ....
    - package.json

```

2. 安装 `shelljs` 包
因为需要简单的shell命令执行

3. index.js实现（比较简单）
```
var shell = require('shelljs')
var images = shell.exec('ls ./images/*', {silent: true}).toString()
var arr = images.split('\n')

var api_key = 'xxx' // 自己申请的apikey
var api_secret = 'xxx' // 自己申请的api_secret
var result = []
for (var item in arr) {
    (function(item) {
        setTimeout(function() {
            var tmp = '@' + arr[item]
            if (tmp == '@') {
                return
            }
            var cmd = 'curl -X POST "https://api-cn.faceplusplus.com/cardpp/v1/ocridcard" -F "api_key="' + api_key +  '" -F "api_secret="' + api_secret + '" -F "image_file=' + tmp + '"'
            var res = shell.exec(cmd, {silent: true}).toString()
            result.push(res)

            if (item == arr.length - 2) {
                for (var index in result) {
                    var res = JSON.parse(result[index])
                    if (typeof res.cards == 'object') {
                       console.log(res.cards[0].name + ' ' + res.cards[0].id_card_number)
                    } else {
                        console.log(result[item])
                    }
                }
            }
        }, item * 1000)
    }(item))
}
```

为什么要写成setTimeout，而不是直接执行？是因为如果一次请求过多，api接口会返回一个`error_message`，说当前接口请求过多，所以每次延时执行，保证不会丢失数据

- 执行`node index`，即会打印出**姓名** + **身份证号**  


