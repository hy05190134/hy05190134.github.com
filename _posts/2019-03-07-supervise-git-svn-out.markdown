---
layout: post
title:  "通过 git/svn 外泄代码的监控"
date:   2019-03-07 11:13:25 +0800
author: hdu.yang
---

* 实际项目中我们需要针对用户通过 `git` 或 `svn` 工具将项目代码外泄的行为进行监控并提供一套统一的解决方案。

* 针对这类需求我们最先想到的是利用 `git` 或 `svn` 工具自带的 `hook` 脚本机制进行监控，但其中存在几方面的原因导致这个方案不可行。第一个原因在于 `svn` 本身没有提供客户端的 `hook` 机制，第二个原因在于即使两者都提供了 `hook` 机制，如果用户本身就包含这些 `hook` 文件，那么覆盖这些文件就不能达到预期的目的，第三个原因在于这些 `hook` 文件还需要保护起来不被其他人修改。综合上述的原因，通过 `hook` 来达到监控的方案不可行。

* 其次我们想到的方案是用 `alias` 的方式，也就是当用户执行 `git` 或 `svn` 的动作时，实际上会先执行我们关联好的脚本，然后再执行真正的 `git` 或 `svn` 动作。但这也存在一些问题，一是如果用户通过全路径执行呢，那么 `alias` 的机制就无法生效了，二是如果用户重新编译了一个叫 `mygit` 这样的命令执行呢，同样 `alias` 的机制也无法生效，三是我们写的 `alias` 脚本同样需要进行保护。

* 目前我们采用的是通过内核+应用层统一控制的方案进行处理的。具体来说就是内核层监测到用户的 `git` 或 `svn` 提交行为，然后通知应用层，由应用层通过命令获取需要上报的信息，因为内核只关心行为本身但不针对协议进行解析处理，否则就会显得耗时间，而这部分的工作交给应用层来处理。
