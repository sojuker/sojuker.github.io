line breaking and word-wrap

在现实项目中，尤其是在测试阶段，鉴于测试使用非常极端的测试用例，我们经常需要“修复”如图所示的这个问题：

- ![长单词行溢出]({{ site.url }}/assets/2012-12-03-a-study-of-text-wrap/1.jpg)

为了修复溢出问题，加入 `word-wrap: break-word;  word-break: break-all;`：

- ![长单词被强行断行]({{ site.url }}/assets/2012-12-03-a-study-of-text-wrap/2.jpg)

word-wrap 和 word-break 究竟是什么？

## 规则

CJK 文字和 !CJK 文字有各自的排版规则。

- CJK ，即东亚文字，代表 Chinese, Japanese, and Korean
- !CJK，即非东亚文字，大多数情况下是西欧文字。

在文字的呈现规则上，两者很不相同。CJK 文字中，一个字母就是一个字素，独立成义；!CJK 文字中，一些字母组成一个字素，并且字素们由连接符“-”连接，或由空格、标点分隔。

CJK 更多的排版规则，较有代表性的是：对中文来说，标点符号不能成为行首（特殊除外，如引号）；双字长的标点符号（省略号、破折号）不能断开。

对于!CJK，主要是：单词不能在中间不合法地断开（合法情况如从连接符处断开）；标点符号不能成为行首（特殊除外）

## 解惑

word-wrap 和 word-break 究竟是什么？对于这个问题，直接拜访 W3C 官方：

找到 CSS3 spec：https://www.w3.org/TR/css-text-3/

### word-wrap（overflow-wrap)

> 单词过长导致当前行溢出时的处理策略

| 属性         | 效果         |
| :---        | :---          |
| normal      | 仅在允许的文本断点处自动换行 |
| break-word  | 如果该行中没有其他可接受的断点，那么将强行断开单词（word）|

### word-break

> 可换行点

| 属性        | 效果        |
|  ---       |    ---        |
|normal      | 根据特定语言自己的规则来决定换行点|
|break-all   | 行为与CJK的normal相同，允许 !CJK 在文本行的任意字内断开，该值适合包含一些非东亚文本的东亚文本|
|keep-all    | 行为与!CJK的normal相同，不允许 !CJK 在文本行的任意字内断开，该值适合包含一些东亚文本的非东亚文本|
|hyphenation |文本会在合适的连字符处("-")断开，需浏览器的支持，默认情况下不断开|

### word-break 效果示例：
![示例]({{ site.url }}/assets/2012-12-03-a-study-of-text-wrap/4.jpg)

### 两个效果的实例：
![实例]({{ site.url }}/assets/2012-12-03-a-study-of-text-wrap/3.jpg)

## 实践方案

- 对于可控的输出内容，无需使用 word-wrap 和 word-break，对于可能产生的长单词溢出这种小概率事件，首先考虑容器宽度是否合理，其次可以为长单词添加连字符“-”以便合理地断开。
- 对于用户输入的内容，比如评论，由于不排除用户会输入“dddddddddddd”这样没营养的垃圾数据，使用 word-wrap: break-word; 来强行截断英文单词，比较美观。

## 观点

- 浏览器默认已经做得够好，强加 break-all 是不优雅的。
- 问题大多不在于实现，而在于设计（如容器太窄）。

> 文章引用自淘宝ued