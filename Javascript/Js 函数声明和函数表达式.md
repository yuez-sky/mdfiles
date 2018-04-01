```
getName()
var getName = function (type){
    console.log('321')
}
getName()
function getName() {
    console.log('123')
}
getName()
```
这个最终结果是什么？


// 函数声明
function xxx() {
}

// 函数表达式, 把一个函数赋值给变量
var xxx = function (){
    xxxx
}

- 函数声明会存在作用域提升的情况，
- 函数表达式进行时复制，需要在声明之后才会被调用
- 函数声明无论是放在被调用后，仍然可以正确使用
```
getName()
function getName() {
    console.log('123')
}
// 123
```
- 函数表达式不行，会报错
```
getName()
var getName = function (type){
    console.log('321')
}
// Uncaught TypeError: getName is not a function
// 去掉var的声明也同样会报错
```


针对上述问题的结果是：
```
123
321
321
```

参考Blog：
> https://github.com/Wscats/Good-text-Share/issues/73




