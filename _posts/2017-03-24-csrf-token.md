非签权接口专注防爬虫，登录接口防暴力破解，鉴权接口防csrf。

因为有了 https 不存在域名劫持，那攻击方要 csrf 就必然跨源，只要跨源就能通过 cors 搞定。除非是 cors 设置 * 的场景

如果是 cors 需要设置 * 的场景（而不是 whitelist），那么登录态的传输必然不能强依赖 cookie（因为场景是完全不同的 domain），需要 http request body 或者 http request header 来传输信息。
但由于登录态的客户端存储依赖会使用 cookie 或 storage 或其他跨源存储方案，csrf 者又会因为跨域(跨源)限制无法获取登录态信息，那么就必然无法通过接口鉴权进而实施 csrf。

（其实还有一种登录态存储方案是在接口端的 domain 存储 cookie，也就是三方 cookie，但应该没有可行性，没见过）

那么就只有最后一种场景，明明不需要 cors * 的场景，设置了 cors * ，同时登录态的储存和传输仍然依赖 cookie，这时候就能通过鉴权，并实施 csrf。
这种场景就是设计有问题，可以通过 csrf token 来解决线上已有问题，但解决成本跟修复成本是一样的，还不如修复问题直接。

所以结论是

1. 如果 document 和 ajax 共享同一父域的场景，用户登录态通过 cookie 存储和天然传输能力，cors 的 allow-origin 设置成 domain + subdomain 就能防 csrf
2. 如果 document 和 ajax 完全不共享域的场景，cors 设置成 *，用户登录态传输通过非 cookie 方式，也能防 csrf
3. 所以 csrf token 似乎真的没有什么用啊

那么问题来了：

当初是基于什么原因 csrf token 被提出来的呢？难道是因为当年的 jsonp 跨域不可限制问题吗？

但 jsonp 可以限 referrer。根据 referrer 的规范，页面内的 jsonp 一般场景都会有 referrer 头（即使 http / https 混杂也是 ok 的），而需要接口交互的场景，也没有设置不跟踪 referrer 的需要，
所以 referrer 几乎所有场景下都会有这个信息，只要有，就能依赖该信息做来源检测。

另外，csrf token 的部署成本也挺高的，生产环境中其实是很少看到这种使用

到底是为什么提出 csrf token 呢？ 推导不下去了。。。
