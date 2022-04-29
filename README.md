# How to build a git server?

> 正如标题所说的，因为项目需要，我想创建一个（像 GitHub 这种的） git 仓库服务器，而这个文档就是用来记录我在这个过程中的尝试和一些经验。

### 背景

我正在写的项目叫 [Open Project](https://github.com/SDU-Open-Project)，简单来说就是一个迷你版的、主要供校内使用的 GitHub。

> 为什么不直接使用现成的 GitHub 或者其它的 git host？有关于 Open Project 的立项论证可以另写一篇文档，在这里就简单概括一下：
>
> - 学校以往举办过很多项目相关的比赛和评审活动。对于学校来讲，这些活动一方面可以提升学生的工程能力，从而提高校外大型比赛中获奖数量，也可以一定程度上提升就业率拓宽出路等等。另一方面，学校和学院可以将这些学生比赛中的实践项目拿去参评优秀学院或者组织之类的，这算是评分中「学生实践活动成果」重要的体现方式。
> - 然而，以往比赛资料收集的方式往往是通过文件传输，例如「将报名表和附件信息发送到 xxx@sdu.edu.cn 邮箱」，再由专人进行整理汇总。往往这个「专人」就是每一届兴趣开放的负责人，而随着他们的毕业这些资料也随之流失。
> - 好一点的方式是把资料上传到云盘上，比如山大云盘或是第三方的云盘服务。但是这些云盘往往是为了文件存储设计的，一方面缺少了项目统一管理，不能与代码管理系统进行交互，多人协作十分困难；另一方面没有展示功能，唯一的预览方式只能是把文件下载下来然后打开，这就增加了评审的难度。
> - 若是让参与者提交到 GitHub 等第三方 host 上，再把项目链接作为报名附件上传如何？且不谈 GitHub 之类第三方服务的安全性，若是项目发生迁移、地址更换、删除，原先的项目链接将无法及时更新。有些private 的项目还需要将第三方评委加入 collaborators 中，而这并不是一件让项目所有者开心的事（指不定哪天评审完就给他踢了）
>
> 综上以及一些没有列举的其它尝试，建立一个我们自己的 git host 是很有必要的一件事。我们也尝试过 GitLab 之类的服务部署方案，但由于可拓展性和一些其它原因，最终确定下来我们从零开始搭建一个项目管理系统。因为是 OpenLab 旗下的项目，定名 Open Project 。

现在已经完成了一些基本的网站服务，例如用户认证系统（目前是基于 JWT 的）、项目的 CRUD ，以及项目成员的 CRUD 系统，并且已经通过了一些基本测试。我希望 git server 是能够跟这套系统相配合的，例如使用现有的用户系统进行身份认证，并且能够根据项目成员的权限来放行或组织 git push 和 pull 操作。因为现在有已经具备这套功能的系统（例如 GitHub），所以肯定是可行的；但 GitHub 服务不开源，所以需要进行一些自己的尝试。
