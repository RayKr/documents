# 关于CATV使用Git代替Svn进行版本控制的可行性分析报告


[TOC]


`Git`与`SVN`都是用于代码版本控制的系统。介于CATV项目组现行的SVN，略过关于版本控制系统的介绍，文本着重讲述Git与Svn的区别以及提升，并确定出替换的可行性。

## Git简介

Git是一款开源的、稳定的、简单高效的分布式版本控制系统。Git在诞生之初便继承了Linux社区简单高效的特性，并在开源环境中不断发展壮大，迅速成为最流行的分布式版本控制系统。

2008年，Github网站上线，它为全世界开源项目免费提供Git存储，无数开源项目开始迁移到Github上，包括著名的PHP,jquery,ruby,nodejs等等。

由于企业源码的保密性，所以不可能存储到Github上的免费库，所以Github提供了付费存储的私有库，但是常常都是公司内部自行搭建Github服务器。现在国内许多大公司也逐步转向Git，例如百度内部，Alipay等现在也在用Git管理源码。

## Git与SVN对比

### 分布式 vs 集中式
Git是分布式的版本控制系统，SVN是集中式的版本控制系统。

“集中式”的版本控制系统，版本库（Repositories）放在“中央服务器”上，工作时每个人都从中央服务器同步到本地，在最新版版本上修改后上传到中央服务器。

“分布式”的版本控制系统，同样有“中央服务器”，更大的特点是可分布式存储。每个人的电脑都是一个完整的版本库，存储更改历史，可提交文件，创建、合并分支等。可见分布式的版本控制系统不再受制于必须有网络时才可工作。和集中式版本控制系统相比，分布式版本控制系统的安全性要高很多，因为每个人电脑里都有完整的版本库，某一个人的电脑坏掉了不要紧，随便从其他人那里复制一个就可以了。而集中式版本控制系统的中央服务器要是出了问题，所有人都没法干活了。

### 分支

Git与SVN的另一个很大不同是分支的使用。SVN虽然支持分支，但是切换和配置十分缓慢，现在已经沦为摆设。但是git的分支快速，简单，并且分支对团队分工合作的帮助十分巨大。

但Git的分支是与众不同的，无论创建、切换和删除分支，Git在1秒钟之内就能完成！无论你的版本库是1个文件还是1万个文件。

### 使用方式和便捷性

Linux上的git使用命令行特别方便，Windows上的`Git for Windows` + `TortoiseGit`可以实现与SVN相同的操作方式，对于熟悉SVN操作的同事们可以无缝切换。

观念需要转换的一点是，commit提交到的是本地的git库中，如果要把本地库同步到中央服务器上，需要进行push->master操作。这也是由于分布式系统所决定的特性，也是需要大家转换的思维。

## 结合CATV分析Git的可行性

接下来，我将逐步根据平常开发中遇到的状况做列举，并叙述git可带来的改观。

> 状况一：Jing和Chen都在开发CyberCloud，虽然各自遵循模块化开发，但不可避免的会在对接上处理相同类，而Jing必须在Chen修改之后的基础上再做修改，遂等待Chen提交更改后的文件到SVN中央服务器。恰恰此时，网络断掉了，Chen无法提交程序，Jing无法继续开发，所有人都被挂起。这时的我们，恨不得可以在局域网内互相推送更新，而不耽误工作进度。

针对上述情况，我相信每个人都碰到过也头疼过，由于SVN的集中式版本控制，把这种情况卡的死死的，而Git却可以轻松应对。Git遵循分布式版本库存储，每个人的电脑就是一个完整的库，包含库的所有信息，每个人都是一个服务器，即使中央服务器挂掉了，数据都清除了，只需要从其中一个人的电脑上clone一遍库即可恢复，在数据安全性也是另一个方面的提升。

状况一所述的问题，自然也就迎刃而解，断网下的情况，局域网内的多人协作不会停止，单人的工作更不会停止。因为每个人都是完整的库，都可以进行commit操作，唯一不能的只是push到中央服务器上而已。而后者完全可以等待网络连通后push一下即可。

PS：SVN无时无刻不得与服务器进行交互，查看log，对比文件，都是在联网状态下进行的，势必效率低，速度慢，而git几乎是一瞬间的事儿。并且像查看log，diff文件这种操作，完全都没有必要联网操作的，徒增麻烦而已。

> 状况二：Jing接到新需求，要开发新CyberCloud新功能模块，虽说新模块，但是在公共js（例如product.common.js）上还是需要改动原有代码。开发过程相安无事，突然今天发现原申购业务中存在某个bug，所以不得不首先解决bug。不巧的是修复bug还需要更改product.common.js，而此时我的代码中已经写入了最新的CyberCloud模块的内容，如果带着新内容上线，则会带来更大的bug。没办法，我当前的解决办法只能是把我修改过的代码保存出来，恢复到以前版本，修复bug，测试提交，再将保存出的内容再写入文件中。

状况二的情况大家都再清楚不过了，也是在开发和维护二合一的工作过程中必不可少的经历。就单单是保存代码片段，修复bug后再次写入这个动作就显得特别的笨拙。并且代码片段的零碎程度和修复bug的出现次数的增加将会大大增加开发过程的“时间复杂度”，简直对开发人员如噩梦一般。

针对这种情况，SVN作为成熟的版本控制软件不是没有想到，解决办法就是分支管理。但是SVN分支创建和切换如蜗牛般缓慢，往往成了摆设，而Git却不同，创建、切换往往一瞬间即可。不得不提一句的是，Git的分支原理是指针的移动，合并的操作也只是HEAD指针从分支中移到master上，所以才会速度如此之快。详细可见[廖雪峰-创建与合并分支](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001375840038939c291467cc7c747b1810aab2fb8863508000)

那么针对状况二，如果我使用git会怎么办呢？首先我在最开始写CyberCloud模块的时候就不在master(主分支)上做改动，master就是只作为最稳定版本源码的分支，任何新的模块和改动都新建分支。所以我就可以新建分支cyber，开始我的模块开发。这时我需要修复bug，没关系，什么都不用动，我只需要把当前工作区`git stash`雪藏起来，`git status`查看是干净的，创建新的bug分支issue-01，修改product.common.js内容，此时的js是基于master的，没有受到我cyber分支的任何影响，所以也不需要保存代码片，修复好bug之后，可以选择保留或删除bug分支。然后我重新检出cyber分支`git checkout cyber`继续工作，完全没有任何影响。

> 状况三：Jing开发了一周的CyberCloud项目，突然接到新需求插队，转而必须进行产品卡未覆盖开发，但是作为一个尚未完全敲定的需求，最后的结果不一定是上线还是舍弃。
所以现在Jing面临以下状况：
- A:CyberCloud我该不该提交到远程仓库，由于更改了product.common.js公共文件，如果提交了，其他人用到了并且比Jing提前上线，又需要把修改过的代码片检出，如果知道还好，如果协同作业的其他人不知道Jing修改过这个文件，直接上线到生产环境，我干保证100%会出问题。如果Jing不提交呢，存在本机上的源码说不定一个小小的意外便丢失掉改动，Jing面临困难抉择。
- B:Jing转而开发的产品卡未覆盖项目，如果最终上线，好的没问题，程序都写完了也都提交了，测试没问题就可以上线生产环境。倘若真的最后要舍弃掉，此时的Jing估计要加班忙碌在检索版本库，恢复版本库，对照版本库上吧。

针对以上状况，Git的分支又可拯救Jing一把。状况A中，SVN因为只存在中央服务器，要么不提交保存在本地但不安全，要不提交保存在远程但可能会干扰他人工作，这就是集中式版本控制系统的坏处。转而看Git是怎么做的，Jing完完全全可以放心的在个人电脑上提交，但不推送到中央服务器上；也可在本地也不提交，只是像状况二中说明的“雪藏”起来，以供随时检出。分布式版本库优势立马展现出来。

状况B中，你可能会说，毕竟没上线到生产环境，在开发环境上就那么放着呗。这是错误也是危险的思想，做开发就是要时刻兼顾生产环境和开发环境的源码同步，不要因为个人不好的习惯影响整个团队的进度。废话不多说，针对这种情况git该怎么处理呢，没错还是分支，可叫做“Feature分支”，顾名思义特性分支。当有一个新特性、新功能，不确定最终舍弃与否，为保整个项目稳定，最好不要在master分支上直接做修改。可以通过创建新feature分支，增添新功能，之后可选择并入master，也可以直接选择舍弃分支。在此期间对他人、对主分支都丝毫没有影响。

以上几点都是我日常工作中出现的比较尖锐的问题，并加以详细叙述。另外一些诸如版本回退、历史记录查看、文件对比、冲突解决、权限控制（git不推崇权限控制，但是放到企业中为保证源码安全，还是可以通过hook（钩子）实现权限控制的。）等功能，SVN能实现的，Git自然也不在话下，此处不再赘述，可详见附录。

## 个人建议的转换过程

当前各个项目都使用SVN进行版本控制，项目数量多，源码容量大，并且所有同事都对SVN产生了习惯性依赖，转变需一步步推进，所以个人推荐转换流程如下：

1. 先搭建我们内网的Git版本库服务器，作为“中央服务器”，用以多人协作同步修改的枢纽，从而不必在工作站电脑中互相推送修改。
2. 由于git和SVN共同使用并不冲突，所以可以先用trinetworks项目作为试点，在同一个文件夹创建.svn和.git库，逐渐理解git理念和操作，孰能生巧，水到渠成。
3. 强烈推荐阅读附录内容，尤其是廖雪峰的Git教程，浅显易懂，适合入门。

## 写在最后
写点个人感受吧，通篇啰嗦了一大堆git怎么怎么好，目的不是说SVN怎么怎么不好。SVN作为一款稳定的、流行的、免费的、修复里CVS遗留问题的集中式版本控制器大行其道是有必然道理的。我只是在学习到了git，并且结合日常工作的情况，做一个比较，推断出我们的团队也许更加适合git的源码管理方式。

昨天偶尔想起git，并且跟领导提了一句，领导说的一句我记忆比较深刻：“可以啊，有好的东西、先进的技术，经过审查确实适合我们的，我们就应该吸收进来，我们也是一个进步的团队。”领导的支持催生出了我今天晚上这篇半瓶子醋逛荡的可行性分析报告，其实根本算不得什么报告，只能算作是用来坚定我内心想法的论据罢了。我认为好的东西，我就要学了来，然后推给大家。不是没有好的工具，不是没有高的效率，有时候只不过是因为我们故步自封。有时候推动车轮前进的，往往都是不满现状的追求。就像是Git，就像是Interliij IDEA.

说起IDEA，我又想说说，IDEA是我在用过了Eclipse、MyEclipse、NetBeans之后确定下来的主IDE工具。我们平常工作的环境无非就是Oracle远程数据库，PL/SQL Developer连接远程数据库、编写和调试SQL语句，使用Oracle Weblogic作为中间件（服务器），MyEclipse作为开发IDE，SVN用来控制源码版本。

可我就是一个不安分的人，Oracle连接远程数据库的时候，一切操作都在远程，干嘛非要在我本机上安装庞大的OracleDatabase呢，就为了配置个tnsname.ora？后来搜索资料发现只需要不足10个文件的`instantclient`（即时客户端）和配置两条环境变量，就可以通过PL/SQL Developer连接到远程数据库了。MyElipse做为一款“强大”的集成化IDE其实并没有给我带来多么好的开发体验，占用资源、内存溢出、编译卡顿、窗口配色等等都让我想转投他人，Interliij IDEA也曾经装过一遍，后来发现不会用又卸载了，最后实在对MyEclipse忍无可忍，强行换了IDEA，花了一个周的研究，配置好了配色、字体、内部集成SVN版本控制、集成Weblogic服务器，Junit单元测试，本来也可以把PL/SQL的功能集成进去，但发现资源消耗非常巨大，所以就放弃了。直到现在我的开发工具就只剩下IDEA和PL/SQL Develpoer了，Weblogic的发布、Debugger调试、SVN版本控制都集成在了一个工具内，简直高效便捷。就如Git对于SVN，IDEA对于MyEclipse一样，也可以承担重任。所以我真的会考虑下一篇文章会推一下Interliij IDEA这款非常优秀的瑕不掩瑜的JAVA开发IDE。

## 附录
1. [《Git和SVN之间的五个基本区别》](http://blog.jobbole.com/31444/)
2. [《Git教程-廖雪峰的的官方网站》](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
3. [《TortoiseGit 使用教程》](http://blog.csdn.net/ethan_xue/article/details/7749639)
4. [《SVN和Git的比较》](http://blog.csdn.net/a117653909/article/details/8952183)
5. [《git-svn — 让git和svn协同工作 》](http://blog.chinaunix.net/uid-11639156-id-3077471.html)
