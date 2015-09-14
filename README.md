# HyMobile
HyMobile：宏奕移动H5单页应用框架

# Overview
- HyMobile是基于seajs、zepto（jquery亦可）建立起来的单页应用框架
- 内置事件机制，分global和module两个级别，可有效解决页面级的事件未销毁带来的问题
- 基于hashchange实现强大而灵活的路由机制，有框架默认的路由规则，亦支持自定义路由规则
- 支持module和action两级构造方法（_init）和析构方法（_destroy），编码更灵活
- 提供了众多常用组件：url、time、cache、view、data等
- 缓存seajs modules到localStorage，大大加快页面的加载速度，减少服务器请求
- URL无权访问到模板模块、_打头的path，如_core、_base等基础modules
- 自动打包样式文件、模板文件为module，框架支持原生HTML、React和doT模板引擎，你也可以按自己的喜好进行扩展

## Tips
- 首先通过npm install安装依赖，使用gulp自动打包样式和模板文件为module
- 使用?hyDebug=1参数开启调试模式
- 业务逻辑文件放在app文件夹，模板文件放在tpl文件夹，样式文件放在style文件夹。通过gulp命令自动包装模板和样式为module，模板文件以tpl_打头，样式文件以css_打头。
- 你可以扩展自己喜爱的模板引擎，也可以引入less、sass等，只需自行完成gulpfile.js即可。

## Config
- 在入口文件，seajs.use加载HY后，通过HY.run方法传入配置参数或者初始化回调方法
- loader-config.js中配置module alias，如 event: core/event.js?c=y&v=150810093722 后面的参数c=y表示缓存该module，v=版本号

# Core Module
核心模块提供了对框架底层的支撑，助力开发更加便捷

## Route
- URL地址+[#!/module/action&arg1=value1&arg2=value2] 如http://localhost:8080/index.html?hyDebug=1#!/play/ssq/run&id=123
- 即xx/xx/xx/.../xx 这组路由规则中最后一个是action，即module暴露的方法，前面的都是module，且与seajs的module概念一致
- 如果不满意现有路由方案，可以配置route模块，或者重写route模块，route模块与hy框架对接使用dist格式：
    {
        rule: '规则',
        module: '需要加载的模块',
        action: '请求的方法'
    }
- Route.reg(rule, dist)实现自定义路由规则，rule支持关键字和正则两种形式，dist直接传对象也可以通过callback返回

## Event
- module级别的：Event.on/emit/off，适合使用当前模块的一些消息传递，切换module的时候自动销毁，无需担心未销毁导致bug
- global级别的：Event.onG/emitG/offG，适合用于整个APP的消息传递，需自行销毁事件，以防止污染

## Cache
- 采用localStorage提供了对缓存的支持，setItem/getItem/removeItem 支持链式写法，因为主要面向移动设备，所以没有兼容不支持localStorage的情况

## Time
- 编码时，应避免依赖本地时间，应该以服务器时间为准，提供now方法返回当前服务器的时间戳（ms）
- format方法提供了对格式化时间的支持 ymdhiswx 年月日 时分秒 星期[0-6] 星期[一到日] 如格式化成2015-08-9 23:16 星期日则传参 'yyyy-mm-d hh:ii 星期x' 

## Data
- query方法，自动带上入口URL中query参数，统一成功和失败的回调，支持缓存

# Structure
- index.html 入口文件
- lib js文件
- lib/loader seajs及配置
- lib/vender zepto及touch
- lib/core 框架核心支撑模块
- lib/base 项目基础模块，如工具类模块
- app/... APP业务模块
- style/... 业务依赖的样式
- tpl/... 业务依赖的模板

# TODO
- ~~处理下seajs加载后的模块缓存到本地，可采用版本的概念处理更新缓存的问题~~
- ~~引入Node.js gulp，将React JSX、dot模板、CSS等自动编译成module~~
- 实现module聚合、混淆、压缩
- 引入Material UI或者 React Bootstrap提供默认的底层UI，并完成loading、dialog、bubble

# FAQ
Q: 从/module1/action1&id=123到/module1/action1&id=321会不会重新执行action1方法

A: 不会

Q: 从/module1/action1到/module2/action2，如何执行构造方法和析构方法

A: module1/action1_destroy -> module1/_destroy -> module2/_init -> module2/action2_init


