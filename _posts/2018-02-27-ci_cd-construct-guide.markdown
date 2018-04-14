## 基于 gitlab-ci 和 docker 搭建 CI/CD

### 概述
* 自动化测试工具对于软件开发来说显得格外重要，特别是针对开发迭代比较频繁的项目来说更有意义。由于目前我们的项目代码都是托管在公司自己搭建的 `gitlab` 上，所以使用 `gitlab-ci` 加 `gitlab-runner` 的方式来搭建持续集成环境比较方便些。由于目前通过 `docker` 方式安装 `gitlab-runner` 具有资源隔离性好，对原有服务器的环境污染少等特点，所以推荐用 `docker` 来进行安装。
* 虽然网上已经有很多相关的文章来讲述如何完成这一项工作，但是*纸上得来终觉浅*，在安装过程中还是会遇到各种各样的问题，下面我会一一说明。

### 原理
* 当我们将最新的代码提交到 `gitlab` 后，`gitlab-ci` 会根据代码分支中配置的 `.gitlab-ci.yml` 文件触发对应注册的 `gitlab-runner` 来编译、构建、测试，最后将运行的结果反馈回 `gitlab-ci` 服务并呈现在 `gitlab` 上。

### 步骤
* 首先第一步自然是安装 `gitlab-runner`（这里省略了 docker 环境的安装），通过执行 
```
docker run -d --name gitlab-runner --restart always -v /var/run/docker.sock:/var/run/docker.sock -v /srv/gitlab-runner/config:/etc/gitlab-runner gitlab/gitlab-runner:latest
```
来安装 `gitlab-runner`，`docker run` 后面的参数的具体含义自行查资料，大致含义就是以 `gitlab/gitlab-runner:latest` 镜像为基础以后台守护进程方式创建名字为 `gitlab-runner` 的容器进程并映射两个相应的目录。当上述命令执行成功后，通过 `docker ps `就可以看到相应的 `gitlab-runner` 服务了。
* 接下来是注册 `gitlab-runner`，通过 `docker exec -it gitlab-runner gitlab-ci-multi-runner register` 命令执行后进入交互模式，输入相应需要的参数则可以完成注册。这里有个坑就是相应的 ci 地址如果是 `http` 的就填写 `http` 的地址，刚开始填写文章中的推荐地址填写了 `https` 就会报错，然后一直在尝试解决这个错误，陷入了困境。
* 注册成功后会在 `/srv/gitlab-runner/config/config.toml` 或者 `docker exec -it gitlab-runner gitlab-ci-multi-runner list` 中看到刚才注册的 `runner` 配置。
* 经过上述步骤后，基于 `gitlab-runner` 和 `docker` 环境的集成测试环境就搭建好了。

### 疑问
* 从上述步骤中我们可以看到 `gitlab-runner` 是用 `docker` 启动的，而每次我们提交代码后，打开 `gitlab-runner` 的界面我们又会看到 `docker` 启动了构建编译的过程，给人的感觉就是 `docker` 里面又跑了 `docker` 程序，而事实上，`docker` 进程中再启动 `docker` 是不现实的。原因主要是两点，一个是与 `LSM(Linux Security Modules)` 有关，另一个是与 `docker` 的存储驱动有关，具体可以参考[文章](https://tieba.baidu.com/p/4063973075?red_tag=1483020092&traceid=)
* 我们看到的假象的原因在于将 `linux` 与 `docker` 之间的 `socket` 通过 `-v` 来进行了映射，相当于在 `docker` 镜像中启动 `docker` 也就是用的外面 `linux` 的 `socket`，也就是并行的关系了，而绝不可能是 `docker in docker` 的方式了。
