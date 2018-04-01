## 目的
为了简化`vue-i18n`国际化实现的小工具。

## 提取vue单文件组件中的汉字
- Nodejs **文件读取**模块`fs`，产生文本字符串，
- 匹配template中的代码，并根据换行划分成数组
- 根据**正则**匹配template中出现的汉字，中文的正则**`[\u4e00-\u9fa5]`**

Nodejs 异步读取文件
```
var fs = require('fs')

var filePath = '../xxx/xxxx/xxx'
var fileName = '/xxx.vue'


let fileString = ''

fs.readFile(filePath + fileName, function (err, data) {
    if (err) {
        return console.error(err)
    }
    fileString = data.toString()
    templateHandler(fileString)
})
```

处理文本字符串

```
function templateHandler(template) {
    // 按换行分成数组
    let arr = template.split('\n')
    if (arr.indexOf('</template>') < 0) {
        console.log('not find the template')
        return
    }
    // 获取html中的template
    let newArr = arr.slice(1, arr.indexOf('</template>'))

    // 获取包含中文字符的片段
    let paragraph = []
    newArr.forEach(e => {
        // 去掉html中的注释
        if (/<!-- .* -->/.test(e)) {
            return
        }
        // 按空格区分
        let lineArr = e.split(' ').filter(item => {
            return item != '' && /[\u4e00-\u9fa5]+/.test(item)
        })
        if (lineArr.length == 0) {
            return
        }
        paragraph.push(lineArr.toString())
    })

    // 去除非中文外的字符，获取中文 和 ：
    let chinese = []
    let result = []
    paragraph.forEach((e,index) => {
        chinese.push(e.replace(/[^\u4e00-\u9fa5：\/]/g, ''))
        replaceArr.push(e)
    })

    // 按：划分，分成单独的词语
    let newCH = []
    chinese.forEach(ele => {
        let tmp = ele.split(/[：\/]/)
        tmp.forEach(e => {
            if (e) {
                newCH.push(e)
            }
        })
    })

    // 中文字符去重
    newCH = [...new Set(newCH)]
    length = newCH.length
    console.log(newCH)
    newCH.forEach(e => {
        translateCNToUS(e, handlerResult)
    })
}
```


## 使用有道Api
- 申请key
- 拼接请求地址url
- 根据返回结果取所需内容
```
var http = require('http')
// md5 需要安装md5的npm包 npm install md5 --save
var md5 = require('md5')

function translateCNToUS(word, callback) {
    // 申请的key和secret
    let Key = '5721fbb81d578037'
    let Secret = 'Kzz4NP0B9rxjncsvyyLXZWz70AvxpGZB'

    // 随机数
    let salt = Math.random()
    // 生成签名
    let sign = md5(Key + word + salt + Secret)

    let baseUrl = 'http://openapi.youdao.com/api'

    // 拼接请求url
    let url = baseUrl + '?from=zh-CHS&to=EN&appKey=' + Key + '&salt=' + salt + '&sign=' + sign + '&q=' + encodeURI(word)
    
    // 发送http GET请求
    http.get(url, function (req, res) {
        var returns = ''
        req.on('data', function(data) {
            returns += data
        })

        // 监听end 获取返回的参数
        req.on('end', function(){
            let result = []
            returns = JSON.parse(returns)
            if (returns.errorCode == 0) {
                result = returns.translation
            }

            // 根据返回结果，去所需进行处理
            callback(word, result[0])
        })
    })
}
```