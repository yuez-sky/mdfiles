Flex 容器中，默认存在两条轴，主轴的方向可以修改
- 水平主轴
- 垂直的交叉轴

容器中的每个单元块都被称为`flex item`，占据主轴的空间为`main size`，占据的交叉轴的空间为`cross size`

不能认`main size`为宽度，`cross size`为高度，因为主轴的方向可以修改


## Flex容器
```
.container {
    display: flex | inline-flex;
}
```
注意：
- 当设置了flex布局后，子元素的float，clear，vertical-aligin的属性将会失效

### 属性
有一下六种属性可以设置在容器上：
1. flex-direction 决定主轴方向
2. flex-wrap 换行
3. flex-flow direction + wrap
4. justify-content 主轴对齐方式
5. align-items 交叉轴对其方式
6. align-content 多条轴线对齐方式

### flex-direction

决定主轴的方向，即项目排列的方向，默认**从左到右**

```
.container {
    flex-direction: row(默认值) | row-reverse(自右向左) | column(自上而下) | column-reverse(自下而上)
}
```

### flex-wrap

决定容器内项目是否可以换行，默认都是排列在主轴线上，使用flex-wrap可以实现项目的换行

```
.container {
    flex-wrapp: nowrap(默认值) | wrap | wrap-reverse;
}
```

- 默认值nowrap： 不换行；当主轴尺寸固定时，出现空间不足的情况，项目的尺寸会随之调整，并不会挤到下一行

- wrap：项目主轴尺寸超出容器时，会换行，第一行在上方

- wrap-reverse：换行，不同于换行，第一行在下方

### flex-flow： flex-direction 和 flex-wrap的简写形式
```
.container {
    flex-flow: <flex-direction> | <flex-wrap>
}
```

### justify-content

定义项目中主轴的对齐方式

```
.container {
    justify: flex-start | flex-end | center | space-between | space-around
}
```

当主轴方向为水平时：
- flex-start 
左对齐

- flex-end 
右对齐

- center 
居中

- space-between 
两端对其，项目之间的间隔相等，即剩余空间等分成间隙

- space-around 
每个项目两侧的间隔相等，所以项目之间的间隔比项目与边缘的间隔大一倍

### aligin-items

定义项目在垂直交叉轴上的对其方式
```
.container {
    aligin-items: flex-start | flex-end |center | baseline | stretch
}
```

当主轴方向为水平时：
- 默认值为stetch
即如果项目未设置高度或者设置为auto，将占满整个容器的空间

- flex-start
交叉轴的起点对齐

- flex-end
交叉轴的终点对齐

- center 
交叉轴的中点对其

- baseline 
项目的第一行文字的基线对其，以文字的底部为主

### align-content 
定义了多根轴线的对其方式，如果项目中只有一根轴线，则该属性不起作用
```
.container {
    aligin-content: flex-start | flex-end | center | spance-between | space-around
}
```

一个轴线？
- 当flex-wrap设置为nowrap，项目不会换行，所以容器中只有一条轴线
- 当flex-wrap设置为wrap， 容器中可能会出现多条轴线，这个时候需要设置多条轴线的对齐方式


## Flex 项目属性
六种属性：
- order
- flex-basis
- flex-grow
- flex-shrink
- flex
- align-self

### order 
定义项目在容器中的排列顺序，数值越小，排列越靠前
```
.item {
    order: <integer>;
}
```

### flex-basis
定义了在分配多余空间之前**项目占据的主轴空间**，浏览器根据这个属性，计算主轴是否有多余空间
```
.item {
    flex-basis: <length> | auto(默认值，即项目本来大小);
}
```
注意：
- 主轴方向为水平时，当设置了flex-basis时，项目的宽度会失效
- flex-basis 需要根据 flex-grow flex-shrink配合使用才能发挥效果


### flex-grow
定义项目的放大比例
```
.item {
    flew-grow: <number>;
}
```

默认值为0，即即使存在剩余空间，也不放大

当所有项目都已flex-basis排列后，仍有剩余空间，那么这个时候flex-grow就会发生作用

如果所有项目的flex-grow的属性都是1，等分剩余空间

如过一个项目的flex-grow属性为2，其他项目为1，则前者占据的剩余空间则比其他项目多一倍

如果所有项目都以flex-basis排列完成后，发现空间不够，且flex-wrap设置为nowrap，此时flex-grow不起作用


### flex-shrink
定义项目的缩小比例
```
.item {
    flex-shrink: <number>;
}
```

默认值： 1，如果空间不足，该项目将缩小，负值对属性无效

如果所有项目的flex-shrink的属性都为1，当空间不足时，都将等比例缩小

如果一个项目的flex-shrink 属性为0， 其他项目为1时，前者不缩小

### flex 
flex-grow、flex-shrink、flex-basis简写
```
.item {
    flex: none | [<flex-grow> <flex-shrink> ? || <flex-basis>]
}
```

- 当 flex 取值为一个非负数字，则该数字为 flex-grow 值，flex-shrink 取 1，flex-basis 取 0%，如下是等同的：
```
.item {flex: 1;}
.item {
    flex-grow: 1;
    flex-shrink: 1;
    flex-basis: 0%;
}
```

- 当 flex 取值为 0 时，对应的三个值分别为 0 1 0%
```
.item {flex: 0;}
.item {
    flex-grow: 0;
    flex-shrink: 1;
    flex-basis: 0%;
}
```

- 当 flex 取值为一个长度或百分比，则视为 flex-basis 值，flex-grow 取 1，flex-shrink 取 1，有如下等同情况（注意 0% 是一个百分比而不是一个非负数字）
```
.item-1 {flex: 0%;}
.item-1 {
    flex-grow: 1;
    flex-shrink: 1;
    flex-basis: 0%;
}

.item-2 {flex: 24px;}
.item-2 {
    flex-grow: 1;
    flex-shrink: 1;
    flex-basis: 24px;
}
```

- 当 flex 取值为两个非负数字，则分别视为 flex-grow 和 flex-shrink 的值，flex-basis 取 0%，如下是等同的：
```
.item {flex: 2 3;}
.item {
    flex-grow: 2;                                                                                                                                                                                                                                                                                                                                                       
    flex-shrink: 3;
    flex-basis: 0%;
}
```

- 当 flex 取值为一个非负数字和一个长度或百分比，则分别视为 flex-grow 和 flex-basis 的值，flex-shrink 取 1，如下是等同的：
```
.item {flex: 11 32px;}
.item {
    flex-grow: 11;
    flex-shrink: 1;
    flex-basis: 32px;
}
```


### align-self
允许单个项目与其他项目不一样的对齐方式
```
.item {
    align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

默认值 auto 表示继承父元素align-items。如果无父元素，则等同于stetch

align-self属性值于align-items是相同的，是对当个项目生效的，而align-items是对容器下所有的项目都生效的



[30分钟flex布局](https://zhuanlan.zhihu.com/p/25303493)