# Html

## Html 语义化

- 正确的标签做正确的事情
- 页面内容结构化
    - 便于浏览器搜索引擎解析（会根据标记确定上下文和关键词的权重，利于SEO）
    - 无Css时也可依文档格式显示，容易阅读

## Doctype & 浏览器渲染模式
### DOCTYPE(Document Type Declaration 文档类型 缩写 DTD)
一个文档类型标记是一种**标准通用标记语言**的**文档类型声明**

目的：告诉浏览器应该用什么样的文档类型定义（DTD）来解析文档

会浏览器的渲染模式产生影响，不同的渲染模式会影响到浏览器对于Css代码甚至Javascript脚本的解析。尤其在IE系列中，由DOCTYPE所决定的HTML页面的渲染模式至关重要

### 渲染模式
- 非怪异（标准）模式
- 怪异模式
- 部分怪异（近乎标准）模式

**标准模式**的页面按照`Html`，`Css`的定义渲染；**怪异模式**尝试**模拟更旧的浏览器行为**；部分怪异模式尝试两者之中妥协的“近乎标标准”模式，实施一种**单元格尺寸**的怪异行为，除此之外符合标准定义

任何一个无`DOCTYPE`的网页都会以怪异模式（quirks）模式渲染

Html5提供的`<Doctype html>`是标准模式，向后兼容的。

## 盒模型
- content-box
    width = width + padding-left + padding-right + border-left + border-right
- padding-box
    width = width(包含padding-left, padding-right) + border-left + border-right
- border-box
    width = width(包含padding-left,padding-right,border-left,border-right)

## Css 普通流（文档流）
元素按照在html中出现的位置决定排布的过程

- margin 用来隔开**元素**与**元素**
- padding 用来隔开**元素**与**内容**

## Css 定位方式
- display属性
    - 常见的是block,inline,inline-block
    - block可以设置宽度，独占一行
    - inline 元素宽度由内容决定，与其他元素并列一行
    - 常见的block： div,h1-h6,ul,li,ol,dl,dt,dd
    - 常见的inline： a,span,em

- position 属性
    - static 默认值
    - relative 设置了相对定位，通过修改top,left,right,bottom，元素会在自身文档流所在的位置上被移动，其他元素则不会调整位置来弥补它偏离后的空隙
    - absolute 相对它最近设置了定位不为static的元素
    - fixed 相对的是可是窗口，即使页面滚动

## Css选择器
- 基本选择器
    - 通配符(*)
    - Id
    - 类
    - 元素
    - 后代(E F)
    - 子元素(E>F)
    - 相邻元素(E + F)
    - 群组(selector1, selector2, ...)
- 属性选择器 
- 伪类选择器

## Css 布局