## 背景：

应对和解决多需求并行开发，且没有统一上线时间的场景（比如技术改造和业务需求并行）。
需要多个分支独立开发，通过合并到一个中间分支部署到测试和预发环境。

## 基础约定

- origin 这里就是指 gitlab，也就是远端分支
- 生产环境的代码部署基于 tag，而非基于 master。
- 禁止主干开发，推行分支开发

## 描述

- 发布用分支（用于部署测试、预发环境），以 release/x.x.x，三位版本命名，程序以此为特征来判断。
- 发布用 tag，以 publish/x.x.x 三位版本命名，程序以此为特征来判断。
- release 分支被 push 到 origin 时，gitlab 将检查，必须满足当前分支领先于origin master，否则push操作失败，并提示开发者需要合并 origin master。
- publish tag 到 origin 时，除了要检测当前 commit 与 origin master 之间的关系外，同时要检查当前 tag 和 origin 上对应 release 分支是否相等，以此来避免任意的 push tag 行为，确保当前发布的是最新的代码。如果不满足上述条件，则 git push失败，并给出提示。
- 若 push tag 成功，gitlab自动将该 tag 合并到origin master，然后删除origin上对应的release分支。
- 新需求开发时，从 orign master 分支 checkout 出本地的dev分支。
- 开发者在 dev 分支上开发，通过 merge 或者 merge request 将代码合并至 release 分支。
- release分支保持相对稳定，多个开发者分支都合并到release分支上，只能通过 release 分支来部署代码到测试或预发环境。
- 个人开发分支，使用 dev/xxx 命名，目前对 xxx 没有命名约束
- 线上发布时使用个人开发分支作为发布单位，所有代码修改都在个人开发分支上进行
- 测试环境部署用分支为 daily，只允许 merge 操作，不允许其他方式的代码提交。
- 预发环境部署用分支为 pre，只允许 merge 操作，不允许其他方式的代码提交。
- master 是稳定代码，是个人开发分支的 baseline（基线）
- 需要部署代码到测试环境时，将个人开发分支的代码合并到 daily 分支上， 并 push 到 gitlab 即可。
- 需要部署预发环境时，将个人开发分支的代码合并到 pre 分支上，并 push 到 gitlab 即可。
- 上线时，在个人分支代码打 tag，并 push 到 origin。tag 命名遵从版本语义规范 publish/x.x.x。
- 个人开发分支必须合并过 gitlab master的代码，由SCM做 git push 拒绝服务来控制。
- 个人开发分支（dev/xxxx）自动合并到 master 之后，SCM及时删除gitlab 中的该分支。
- daily 和 pre 是 n 个开发分支的组合，目前采用人工合并，很不灵活，一旦组合变化，就需要新建对应 daily 和 pre。如果依赖于程序自动化，又会存在自动 merge 引发的问题不容易察觉的结果。
