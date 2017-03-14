## 作用域

作用域，是指一个变量在某个特定范围内可以被访问到，而在该范围外无法被程序访问。
在Javascript中，简而言之，有全局作用域和函数作用域。

全局作用域，对应全局对象global。所有定义在全局对象上的属性和方法，同时也是全局作用域中的变量和函数，反之亦成立。

在Js中声明函数，当函数被执行时，程序将进入到函数的作用域中(**函数作用域**)。
此时，在函数中可以访问到函数所产生的作用域和全局作用域。
也就是说，在函数内部既可以访问到函数内声明的变量、函数、arguments，
也可以访问在全局作用域中的变量和函数。
函数的每次调用都将产生不同的作用域，不同作用域间的变量不会产生相互影响。

### 作用域链

在Javascript中，函数可以嵌套函数。也就是说，函数作用域可以嵌套更深的函数作用域。
当作用域发生嵌套时，Javascript使用链式结构来表示这种关系，程序上称之为作用域链。

作用域链使得程序可以对祖先作用域中的变量进行读写操作（但无法进行删除和新增操作）。

### 作用域的屏蔽效应

在作用域中进行变量的读写操作时，程序是从当前作用域为起始，沿着作用域链递归查找同名变量的过程。如果发现同名变量，就中断这个过程，并以此进行读写操作；如果无法找到同名变量，在进行读取操作时，程序会报错; 在进行写入操作时，则会自动将该变量声明为全局变量。

由于变量查找是一个递归查询过程，因此会产生作用域的屏蔽效应，即出现同名变量时，程序无法读写更深层次的同名变量。在程序开发中，需要留意这个特性带来的 `side effect`。

### 作用域共享

如果不同作用域共享相同的祖先作用域，在作用域A中对祖先作用域C中的变量b进行write操作。然后，在作用域B中读取其祖先作用域C中的变量b，返回值会受到前面write操作的影响。

```javascript
function C(){
    var b = 0;

    function A() {
        b = 1;
    }

    function B() {
        alert(b);
    }

    window.A = A;
    window.B = B;
}

C();
A();
B();
```

程序开发中，要极力避免这种不直观的场景。

### 闭包哇哈哈

从上例中可以看到，拥有相同祖先作用域的不同函数之间，是能够共享这个祖先作用域的。

实际上，同一个函数，每次执行时，其父级和祖先作用域也是共享的。这是由于 Javascript 是基于词法作用域的，也就是说，其父级和祖先作用域在函数被声明时即被确定，且无法修改。 这有别于动态作用域，其父级和祖先作用域仅在函数被执行时才被确认。

从 Javascript 的语言实现来看，每个函数在声明时，都拥有一个 `[[scope]]` 属性。该属性是一个链式结构，链式结构中的每个节点代表一个作用域。链式结构的顺序以函数声明时所在作用域为起点，依次向上，以全局作用域为终点。

广义的闭包是指拥有词法作用域特性的作用域创建者。在Javascript中，就是函数，函数自身拥有词法作用域，并且也能产生作用域。
狭义的闭包，也就是常说的闭包，是指在函数内部的函数。这就是闭包的作用，提供对某些隐私作用域的访问权限，实现隐私数据的共享行为。

## 3. Javascript 实现继承

### 怎么来继承

简单而言，基础的继承实现基于原型式继承，还有 all-in-ones 的继承方式（复制、mixin、函数借用）

### 类式继承

```javascript
Function.prototype.implement = function(superClass) {
    // 实现继承链
    function F(){};
    F.prototype = superClass.prototype;
    this.prototype = new F();

    // 修复 constructor 属性 和 增加对父级原型的访问权限
    this.prototype.constructor = this;
    this.prototype._superProto = superClass.prototype;
};

// 构造函数A
function A() { this.name = "aa"; }

// 构造函数B
function B() {}
B.prototype.test = function (){ console.log(456); }

// 在A上实现B的接口，即A继承于B
A.implement(B);

var a = new A();
console.log(a.name);        // aa
console.log(a.test());       // 456
console.log(a.constructor === A);           // true
console.log(a._superProto === B.prototype); // true
console.log(a._superProto.test === a.test); // true
```

### all-in-ones

有点懒，暂时就先忽略一下。

### 基于复制的面向对象编程

```javascript
Object.prototype.implement = function (obj) {
    // 这里写的浅复制，没来得及写成深复制。
    for (var key in obj) {
        if (obj.hasOwnProperty(key)){
            this[key] = obj[key];
        }
    }
}

var promise = {
    onFulfill: function() {...},
    onReject: function() {...},
    fulfill: function() {...},
    reject: function() {...},
    status: "pending"
};

var obj = {};
obj.implement(promise);
```

## 4. Event 事件机制

### 事件模型（职责链 + pub/sub）

三个阶段（按照DOM树传递事件），订阅者/发布者模式。

```
EventTarget.addEventListener()
EventTarget.removeEventListener()
EventTarget.dispatchEvent()
```

### 事件对象  `Event`

事件对象向用户提供了必要的用户交互信息，开发者根据这些用户信息来控制程序的逻辑走向。

```
// 事件所处阶段
e.eventPhase

Event.prototype
    CAPTURING_PHASE : 1
    AT_TARGET       : 2
    BUBBLING_PHASE  : 3

// 事件类型
e.type

// 当前DOM节点
e.currentTarget

// 事件源DOM节点
e.target

e.stopPropagation()
e.stopImmediatePropagation()

e.preventDefault()

// 更多信息可参见相关文档或规范定义
```

### 默认行为

从测试效果看，可理解为，对应触发事件后，默认行为 function() { //dosomething} 立刻进入到 event loop 队列中，而 preventDefault 函数的调用是将该默认行为在队列中移除。可以类比 `setTimeout` 和 `clearTimeout`。

此概念也常用于Javascript编程中。经典的例子 ，dialog 对话框在关闭前触发一个 beforeClose 事件，其默认行为即关闭对话框。 但是当事件处理函数返回 false 时，阻止默认的对话框关闭行为。

### jquery的事件实现

 1. 事件的手动派发(派发该元素上的事件)
 2. 事件对象的二次封装
 3. 事件委托的模拟
 4. 非冒泡事件的冒泡化（职责链派发）
 5. 高阶事件（特殊事件）

## 性能优化系统

- 性能监控
    1. 监控方式（侵入式和非侵入式）和埋点
    2. 监控粒度（关键时刻点）
    3. 数据运算和图表化
    4. 基础服务化，快速接入系统
- 数据分析
    1. 分析性能瓶颈
- 方案调研和优化
    1. 确认性能瓶颈，调研解决方案，设计、尝试、权衡
    2. 代码开发与上线
- 优化反馈
- 流程化，上下游打通，后端优化，数据化，产生积累效应，推广监控的范围
- 性能优化建议
    1. CDN接入，提升**请求并发**能力（网络延时高时有较好效果）
    2. 降低资源冗余度，**按需加载**
    3. 合理的版本管理，提升**缓存**命中率
    4. 合理的请求合并（或资源嵌入），**降低请求数**
    5. 延迟加载（考虑竞争状态），**核心任务优先**原则
    6. **提前处理**（DNS预解析，图片预加载，js预加载）
    7. HTTP长连接，**避免重复握手**


## 前端架构

- Javascript 基础库，比如 jquery，underscore 等
- UI 组件库，比如 lazyload、dialog、cookie 等
- reset CSS & normalize CSS & util CSS & mixin LESS
- CSS预处理语言 和 后处理工具
- Javascript模块化编程
- 模块化开发
- 本地构建，代码部署和发布（CDN）
- 注释 和 文档化

## FIS 的核心功能

- 模块化开发，依赖声明
- 后端 loader 实现 和 map.json 资源依赖表
- 三种能力，分离开发和部署
- 覆盖式发布和md5戳，最大限度利用缓冲
- 将性能优化纳入流程，并自动化


### 判断字符串组成

第一个必须是字母，后面可以是字母、数字、下划线，总长度为5-20

考察正则表达式的掌握。
```
var regexp = /^[a-zA-Z]\w{4,19}$/ ;
regexp.test("tetttttssfgdsg8979");
```


### 截取字符串 abcdefg 的 efg

考察字符串操作API的熟悉。

- `String.prototype.indexOf()`
- `String.prototype.substring()`
- `String.prototype.substr()`
- `String.prototype.test()`


### 判断一个字符串中出现次数最多的字符

字符串遍历，并进行字符出现的计数，考察基本编程知识。


### 如何规避多人开发时的函数重名冲突

- 命名空间；
- 立即执行函数模式，避免污染全局环境；

样例代码：
```
MYAPP.namespace('MYAPP.utilities.array');
MYAPP.utilities.array = (function() {

    // 依赖声明
    var uobj = MYAPP.utilities.object,
        ulang = MYAPP.utilities.lang,

        //私有属性
        array_string = "[object Array]",
        ops = Object.prototype.toString;

    //私有方法
    function isArray(a) {
        return ops.call(a) === array_string;
    }

    //一次性初始化过程
    //.........

    //公有API
    return {
        isArray: isArray
    };
}());
```


### 面向对象编程中的继承

- 原型式继承 和 借用构造函数结合的方式。

```
function inherit(SubClass, SuperClass) {
	SubClass.prototype = Object.create(SuperClass.prototype);
	SubClass.prototype.constructor = SubClass;
	SubClass.uber = SuperClass;
}

function SubClass(config){
	if (SubClass.uber) {
		SubClass.uber.call(this, config);
	}
	//......
}

function SuperClass(config){
	//......
}

inherit(SubClass, SuperClass);

var obj = new SubClass({});
```

借用构造函数方式，解决了实例属性的问题，同时可以灵活地传递参数；原型式继承的优势，是有效地实现原型成员的复用和扩展（覆写），同时维护了原型链的关系。

### 求字符串的字节个数

在GBK编码下，中文是两个字节，UTF-8下中文为三个字节。

```
function GetBytes(str){
    var len = str.length;
    var bytes = len;
    for (var i = 0; i < len; i++) {
        if (str.charCodeAt(i) >= 256) bytes++;
        if (str.charCodeAt(i) >＝ 65536) bytes++;
    }

    return bytes;
}
```

### 数组中相同元素去重

- 注意要创建数组副本，不要修改原始数组。
- 注意尽量避免扩展内置类型的原型方法。
- `indexOf` 和 `equal`，如何判断两个元素相等

```
Array.prototype.unique = function() {
    var ret = [];
    var obj = {};
    var len = this.length;
    for (var i = 0; i < len; i++) {
        var val = this[i];

        if (!obj[val]) {
            obj[val] = true;
            ret.push(val);
        }
    }
    return ret;
};
```

### this 的典型应用

- this 默认指代了 window
- call 和 apply 的应用
- 函数被当做方法调用
- 构造函数

### 实现对象的深复制

- 实现浅复制。
- 递归即可。
- 函数是不可复制的，因此只能针对数据。

### 如何理解闭包

先说作用域，作用域是指可以程序可访问的变量和函数的集合（权限） 。在JavaScript中，函数拥有自己的作用域。 由于函数中可以嵌套函数，因此作用域中也可以嵌套作用域。

以下为例：

```javascript
function a() {
	var A = 0;

	function b() {
		var B = 1;
	}

	b();
}
```

函数 a 中嵌套了函数 b。

函数 a 被执行时，将创建自己的作用域。因此在函数 a 的执行环境中， 可以通过变量标识 `A` 、`b` 访问到自身作用域中的对应变量和函数。同理，函数 b 被执行时也将创建自己的作用域（每次执行都会创建新的作用域），函数 b，不仅可以访问自身作用域中的变量和函数， 也可以访问其外层作用域中的变量和函数。

这是由于，javascript 的变量和函数的查询机制决定的。 当存在作用域的嵌套情况时，此时，我们将从最里层的作用域开始，一层一层到最外层的一系列作用域所形成的一个有序数据结构，称之为作用域链。在函数中引用一个变量，该变量将沿着当前的作用域链，按照就近原则，进行匹配查找。因此，里面的函数，可以访问外层函数的作用域。

函数的作用域链由两部分组成，一部分是**执行时作用域**，另一部分则是函数被定义时所确立的**静态作用域链**。当函数被定义时，其静态作用域链就已经被确定，不论函数在哪里被调用，其静态作用域链的值就是当前所在函数的**执行时作用域链**。函数调用时，在其创建执行上下文阶段，将**执行时作用域**拼接在其**静态作用域链**的前端，才形成**执行时作用域链**。这样的实现，使得在函数内部，开发者既可以访问定义在该函数内的局部变量和函数，也可以访问外层函数以及全局作用域中所定义的变量和函数。

闭包是什么呢，广义的闭包就是指这个作用域机制。狭义的，是指函数拥有闭合的作用域，除了在该函数体内以及在该函数体内所定义的函数内可访问该作用域以外，其他方式均不可访问该作用域。

这个特性，经常被用来创建一些中间作用域，用于限制变量和函数的访问权限。

名词解释：

- 函数在被定义时，拥有`[[Scope]]`属性，用于存放该函数的作用域链。
- 函数被定义时，JavaScript引擎自动将该作用域链的值复制为所定义的执行上下文的作用域链值。
- 函数作为第一类对象，是指函数可以像变量一般作为实参、函数返回值的形式被传递。
- 执行上下文（Executive Context）是指可执行代码（Global、Function、Eval）执行时所创造并依赖的上下文。
- JavaScript中有全局作用域、函数作用域、动态作用域。
- 动态作用域是指临时作用域，分为 `with`、`try-catch`、`eval`三种形式。

## Promise

```javascript
var a = Promise.resolve(1);
a.then(() => {})
    .then((res) => { console.log(res); return 2;})
    .then(null)
    .then((res) => { console.log(res); return 3; })
    .then((res) => {
        return fs.readFile("./someFileDoNotExist.js", "utf8", (err, content) => {
            throw err;
        });
    }).then((res) => {
        console.log("4");
        throw new Error("6");
    }, (err) => {
        console.log("5");
        return JSON.parse("{abc: 567}");
    }).catch(function(err) {
        console.log(err.message);
    });
```

## 如何定义类型，prototype 原理

在JavaScript中，通过构造函数，实现特定类型的对象创建。

当我们在定义函数时，JavaScript引擎会自动定义该函数的构造器逻辑，构造器逻辑在我们使用 new 操作符调用函数时被调用。此外，JavaScript引擎还会自动新建该函数的 `prototype` 属性，其值是一个新建的Object实例，并拥有 `constructor` 属性，指向我们所定义函数。

实例对象在被通过 new 操作创建时，JavaScript引擎将执行一个操作——给新建的实例对象赋值`[[prototype]]`属性，该值指向其构造函数的 `prototype` 对象。这时，我们将 `prototype` 对象称之为该构造函数的原型对象。通过这一机制，实例对象与原型对象建立了联系，实例对象一旦创建，其指向的原型对象就已确定，此时，如果更新构造函数的`prototype`对象的成员，在实例对象也会同步更新。但如果将构造函数的`prototype`指向为另一个对象，则原先创建的实例仍指向旧原型对象，之后创建的实例指向新原型对象。

接下来就说 原型链 和 原型链查找机制。

### 前端开发的优化

原则一： 减少网络通信和HTTP请求消耗

- 合并请求，比如CSS精灵、合并JavaScript文件、合并CSS文件
- 缓存数据，减少请求
- CDN，选择最优线路下载数据
- 按需加载，仅当需要时才请求必要数据
- 延迟加载，典型应用是Lazyload延迟加载图片
- 预加载，提前申请数据，对用户未来行为预测
- 代码优化，减少代码量
- 首屏外数据异步化

原则二： 减少客户端的处理压力

- 合理规划HTML代码，精简DOM树规模
- 利用CSS选择器性能差异，尽可能少用CSS表达式、利用CSS继承和层叠特定
- img标签在宽高可知时在HTML中定义宽度和高度，避免浏览器回流和重绘
- 减少和优化DOM操作，缓存查询结果，批量化操作方法避免反复回流和重绘（文档碎片、复制节点、隐藏元素）
- 使用setTimeout避免浏览器失去响应，或者使用 Web Workers后台执行JavaScript
- 使用innerHTML，利用浏览器的原生效率高的特性
- 使用事件代理技术
- 延迟渲染，比如textarea的技术
- 优化JavaScript代码，减少性能损失

具体规则可以看Yahoo的指导建议。