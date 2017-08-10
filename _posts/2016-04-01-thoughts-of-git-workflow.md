read this: https://docs.gitlab.com/ee/workflow/gitlab_flow.html

## git without workflow:

The biggest problem they run into is that many long running branches that each contain part of the changes are around. People have a hard time figuring out which branch they should develop on or deploy to production.

## git flow

It advocates a master branch and a separate develop branch
as well as supporting branches for features, releases and hotfixes.
The development happens on the develop branch,
moves to a release branch and is finally merged into the master branch.

Git flow treats releases and hotfixes separately. This works finely in a less frequent project that a new release version often takes more than one weeks, so that hotfixes are needed to fix issues in time. Develop branch is the branch that all developers start coding off, releases and hotfixes branches should always merge into develop and master branch to keep track the correct status of the repos. As for master branch, it plays a role of the production branch. Release branches support preparation of a new production release. The key moment to branch off a new release branch from develop is when develop (almost) reflects the desired state of the new release. And the corresponding release version is determined until that release branch is created.

## github flow

> One of the bigger issues for me is that it(here means git flow)’s more complicated than I think most developers and development teams actually require. It’s complicated enough that a big helper script was developed to help enforce the flow.

Github flow has only feature branches and a master branch.
Github flow has a simplified workflow, and works well in a small team with a more
frequent published project, this is the situation that we encounter fairly often.
There is no difference in the GitHub flow between a hotfix and a very small feature.

## gitlab flow

> But this flow still leaves a lot of questions unanswered regarding deployments, environments, releases and integrations with issues. With GitLab flow we offer additional guidance for these questions.

merging first, deploy second.

> Continuous integration: In software engineering, continuous integration (CI) is the practice of merging all developer working copies to a shared mainline several times a day. CI was intended to be used in combination with automated unit tests written through the practices of test-driven development. Later CI introduced build servers, which automatically ran the unit tests periodically or even after every commit and reported the results to the developers.
> Continuous integration - the practice of frequently integrating one's new or changed code with the existing code repository

gitlab flow 适用于 ci 和 自动化测试，因此可以将 feature 分支划分成很小，并且实现快速的代码修改和自动验证。
对于一般软件工程而言，ci 和 自动化测试程度并不会高度发达，在测试上较为依赖人工测试，
这时候如果 feature 分支拆得很小，就会有更频繁的 merge request 与对应的测试验证工作，否则就丧失了 ci 的意义，
目前许多公司的软件工程程度都很难达到这种程度。

In GitLab it is common to protect the long-lived branches (e.g. the master branch) so that normal developers can't modify these protected branches. So if you want to merge it into a protected branch you assign it to someone with master authorizations.

## a workflow should consider

- 什么时候要 merge，以及怎么 merge
- 测试环节放在哪里，特别在人工测试的情况下
- 尽早集成（这里的集成就是功能组合的意思）是一个大原则
