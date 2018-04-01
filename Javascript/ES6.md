

## 函数
- 函数参数的默认值
```
function foo(a, b=1) {}
```
- rest 参数
```
function foo(...rest) {}
```
- 严格模式
- name 属性
```
function foo() {}
foo.name // 'foo'
```
- 箭头函数
    - this为定义时绑定，而非运行时绑定
    - 不能作为构造函数，即不能使用`new`
    - 没有`arguments`参数
    - 函数内部不能使用`yeild`,即不能是`Generator`
```
// 简化了书写方式
() => {}
```
- 双冒号运算符
- 尾调用优化
- 函数参数的尾逗号

## 数组
- 扩展运算符
```
...[1,2,3]
// 1,2,3
```
- Array.from()
```
// 两类对象转为真正的数组：
//    类似数组的对象（array-like object）
//    可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map
```

- Array.of()
```
Array() // []
Array(3) // [ , , ]
Array(1,2,3) // [1, 2, 3]

Array.of() // []
Array.of(3) // [3]
Array.of(1,2,3) // [1,2,3]
```


- 数组实例的 copyWithin()
```
copyWithin(target, start, end)
// 将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。
// 也就是说，使用这个方法，会修改当前数组。

[1, 2, 3, 4, 5].copyWithin(0, 3, 3)

```




- 数组实例的 find() 和 findIndex()
```
find() // 返回时实际例子
findIndex() // 返回索引
```


- 数组实例的 fill()
```
// 填充数组
fill(填充的字符串, [开始位置[, 结束位置]])
```


- 数组实例的 entries()，keys() 和 values()
```
for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"

let letter = ['a', 'b', 'c'];
let entries = letter.entries();
console.log(entries.next().value); // [0, 'a']
console.log(entries.next().value); // [1, 'b']
console.log(entries.next().value); // [2, 'c']
```


- 数组实例的 includes()
```
// 确定是否包含某个值
```


- 数组的空位
```
// 数组是否包含某个值
[ , , ] // 空位
```

## 对象
- 属性的简洁表示法
- 属性名表达式
- 方法的 name 属性
- Object.is()
- Object.assign()
- 属性的可枚举性和遍历
- Object.getOwnPropertyDescriptors()
- __proto__属性，Object.setPrototypeOf()，Object.getPrototypeOf()
- super 关键字
- Object.keys()，Object.values()，Object.entries()
- 对象的扩展运算符