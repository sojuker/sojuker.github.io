又经历了一个月的重构

## FIS2

这次重构项目用上了 FIS2， 瞬间就上了档次。相对 FIS1 来讲，FIS2 更强调三种能力，从设计思路到文档表达上，也更加成熟。

FIS2的三种能力，资源定位、资源依赖、资源嵌入。
最基本的要素，是资源定位能力，FIS在对项目文件静态编译时，通过特征标识符(`src`、`url`、`__uri`等)来识别资源路径，并通过对相对路径 或 根路径(root) 的解析去尝试定位资源。如果对应路径的资源存在，则三种能力最终转换为 资源的真实发布路径，资源的真实内容（即嵌入）以及 资源映射表（即资源间的依赖）。

## 子系统划分

子系统划分的目的，在于将大系统各部分进行有效隔离，降低关注度，提升对代码的控制能力。
在子系统内，可以有效地提升代码的复用度和维护性，独立维护子系统内的生态系统。
业务子系统间仅通过URL方式进行通信，避免其他的干扰，使得程序边界更加清晰。

同时，抽离一个公用子系统，对各业务子系统提供复用功能，以此解决复用问题。

在公用子系统中，存放开发的基础类库、js模块加载器、基础CSS、可复用CSS，公用组件等。

## HTML

主要内容就是 HTML template。放置的内容包含

* HTML5 mobile 标准头部
* 基础 CSS 和 JS
* 错误监控脚本
* 性能监控脚本
* 数据统计脚本

## CSS

FIS2 使用了开源的LESS。本来希望引入autoprefixer，由于时间仓促，仅仅完成了调研，并没有投入使用。

CSS的分层模式为:

* reset/normalize.css(统一不同浏览器差异)
* util.css(实用的类，全站通用)
* mixin.less(网站配色 + 浏览器兼容 + 实用函数 + 字体排版)
* widget.css(组件级别，限制样式的作用域)
* page.css(页面级别)

## js基础类库 和 模块化管理库

使用 zepto 作为我们的基础库，除核心 zepto.js dom 外，也引入 ajax、event、form、touch 等子模块。

由于时间仓促，仍沿用 gmu 封装的zepto库（修复了部分bug），版本比较老。为了充分享受开源社区的成果，以后也考虑使用最新的zepto版本。

js模块化和加载，使用 fis 自带的 modjs， modjs 天生与 fis2 的 map.json 配合，开发成本上更小。

## UI库 和 js基础模块

FIS1的时候，使用的是GMU(移动开发UI库)。后来因为某些原因，放弃使用，而是自己开发。
自己开发的好处是，可控性强且能针对业务逻辑进行优化，但在测试覆盖上做得薄弱。
这次重构中，主要引入 lazyload、toast、dialog、suggestion 等UI组件。
除UI组件外，还重构了 cookie、http、storage、geolocation、openapp 等js基础模块。

## 开发与测试基准

- 两种平台：android & IOS
- 系统版本支持：
    - android 2.3.x
    - android 4.x
    - IOS5
    - IOS6
    - IOS7
- 浏览器覆盖：
    - 系统默认浏览器(android 和 IOS)
    - UC浏览器
    - QQ浏览器
    - 百度浏览器
- 网络状况：
    - 2G
    - 3G
    - 4G（目前与3G同等对待）
    - wifi
- 屏幕宽度
    - 320px - 400px

[参考1:  国内浏览器使用率](http://www.cnnic.net.cn/hlwfzyj/hlwxzbg/ydhlwbg/201310/P020131016356661940876.pdf)

## 开发规范

- 目录组织和文件命名规范
- HTML + CSS规范   (*参考：ecomfe、google、tweet)
- JS 规范          (*参考：ecomfe、google、jsdoc)
- FISP 使用规范    (基于业务实践)