# Swoft

## 介绍
基于Swoole的PHP框架。

> [项目GitHub地址](https://github.com/swoft-cloud/swoft)

## 安装
- 手动clone项目，Composer安装依赖
- Composer安装`composer create-project swoft/swoft swoft`
- Docker 安装`docker run -d -p 80:80 --name swoft swoft/swoft`
- Docker-Compose安装 通过配置docker-composer.yml文件来安装[官网地址地址](https://doc.swoft.org/master/zh-CN/quickstart/development.html)

采用Docker方式安装：
```
# 安装
docker run -d -p 8001:80 --name swoft swoft/swoft

# 能够看到已经在运行的容器
docker ps

# 通过访问 localhost:80 能够看到swoft的界面

# 进入swoft容器
docker exec -it swoft bash

# 如果第一步安装很慢，可以使用docker加速源
# 我的DaoCloud加速源 http://796958e9.m.daocloud.io
# Docker -> Perferences -> Daemon -> Register Mirrors
```

## 生命周期
### 框架生命周期
基于`Swoole`

### 请求生命周期

####  HttpServer

`ServerDispatcher`负责调度，参与者：
- `RequestContext` 请求上下文 贯穿这个请求周期，负责信息储存，传递
- `ExceptionHandler` 异常处理器 
- `RequestHandler` 请求处理器 也就是中间件`Middleware`，贯穿`Request -> Router -> Controller -> Action -> Renderer -> Response`，只要任意环节返回`Response`则整个请求返回

#### RpcServer
`ServrDispatch` 负责调度，但是参与者只有：`RequestContext`，`RequestHandler`。区别较大的是`RPC Server`下有`ServicePacker(数据打包器)`，负责统一返回数据格式

## 配置


## Bean容器
`bean` 一个类的一个对象实例

`容器` 工厂，存放管理`bean`的生命周期

### 注解

#### `@Bean`
- name 定义bean别名，缺省默认**类名**
- socpe 注入bean类型。默认单列，`Scope::SINGLETON/SCOPE::PROTOTYPE`
- ref 指定引用bean，用于定义在接口上面，指定使用那个接口实现

#### `@Inject`
- name 定义属性注入的bean名称，缺省属性自动**类型**名称 


### 定义Bean
两种定义方式，**注解**和**数组配置**

#### 数组定义
```
$beanConfig = [
    'class' => MyBean::class,
    'pro1'  => 'v1',
    'pro2'  => 'v2',
    [ // 构造函数参数
        ‘arg1’,
        '${beanName}'

    ]
]

```
- 数组中必须要有class字段定义
- pro1 pro2 和类的成员变量名称是一一对应
- 属性值和构造函数参数值，都可以通过`${xxx}`和`${config.xx}`，注入Bean和引用properties配置信息


#### 注解定义
使用PHP文档注解，在类上做**标记**，通过解析类注解，实现不同功能
```
/**
 * @\Swoft\Bean\Annotation\Bean('userData')
 */
class XxxBean
{
    
}
```

### 操作Bean
```
App::getBean('name');
ApplicationContext::getBean('name');
BeanFactory::getBean('name')
BeanFactory::hasBean('name');
```
- App/ApplicationContext/BeanFactory 都可以从容器中得到`Bean`
- hasBean 确定某个`Bean`是否存在

### 实例
#### 别名定义
```
/**
 * @\Swoft\Bean\Annotation\Bean('userData')
 */ 
class UserData 
{
    public function getData()
    {
        return []
    }

}

/**
 * @\Swoft\Bean\Annotation\Bean()
 */
class UserLogic
{
    /**
     * @\Swoft\Bean\Annotation\Inject("userData")
     * @var \UserData
     */
    private $userData

    private function getUser()
    {
        return $this->userData->getData();
    }
}

```

#### 缺省定义

```
/**
 * @\Swoft\Bean\Annotation\Bean('userData')
 */ 
class UserData 
{
    public function getData()
    {
        return []
    }

}

/**
 * @\Swoft\Bean\Annotation\Bean()
 */
class UserLogic
{
    /**
     * @\Swoft\Bean\Annotation\Inject()
     * @var \UserData
     */
    private $userData

    private function getUser()
    {
        return $this->userData->getData();
    }
}
```


#### 接口引用
```
/**
 * @\Swoft\Bean\Annotation\Bean('boy')
 */
interface UserInterfalce
{
    public function getData();
}

/**
 * @\Swoft\Bean\Annotation\Bean('boy')
 */
class UserBoy implements \UserInterface
{
    public function getData()
    {
        return 'boy';
    }
}

/**
 * @\Swoft\Bean\Annotation\Bean('girl')
 */
class UserGirl implements \UserInterface
{
    public function getData()
    {
        return 'girl';
    }
}

/**
 * @\Swoft\Bean\Annotation\Bean()
 */
class UserLogic
{
    /**
     * @\Swoft\Bean\Annotation\Inject()
     * @var \UserInterface
     */
     private $userData;

     private function getUser()
     {
         return $this->userData->getData()
     }
}
```
- 接口上面指定了使用的实现bean别名
- 接口使用处，无需指定使用的那个别名，会根据接口上面的引用注入不同的实列bean


## 应用组件
采用组件化开发，自由组合各个组件满足业务实现。

## 事件管理
### 基本使用
```
use Swoft\Event\EventManager;

$em = new EventManager;

// 注册事件监听
$em->attach('someEvent', 'callback_handler');

// 触发事件
$em->trigger('someEvent', 'target', ['more params']);

// 
$event = new Event('someEvent', ['more params']);
$em-trigger($event);

```

### 事件分组
推荐相关的事件，在名称设计上进行分组

### 事件通配符`*`
```
# 全局所有
$em->attach('*', 'gloabal_listener')

# 带前缀
$em->attach('db.*', 'db_listener')

# 到达监听器前厅停止了本次的事件传播
$em->stopPropagation(true);

```

## 连接池


## AOP面向切面编程
AOP(Aspect Oriented Programming) 面向切面编程
> 程序运行时，动态的将代码切入到类的指定方法、指定位置上的编程思想

## 异常处理
## 命令行
## HTTP服务
## RPC服务
## 缓存
## 数据库
## 视图
## Session会话
## RPC客户端
## HTTP客户端
## 任务
## 进程
## 国际化
## 日志
## 内存操作
## 服务治理
## 开发者工具
