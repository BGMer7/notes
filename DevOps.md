# DevOps

## CI/CD

### Jenkins & GitLab CI/CD

[Jenkins vs GitLab CI：CI/CD工具之战_Kubernetes中文社区](https://www.kubernetes.org.cn/8489.html)

在过去十年的时间里，持续集成（CI）和持续交付（CD）取得了巨大的进步。DevOps的兴起导致对CI/CD工具的需求，也快速增长。现有的CI/CD解决方案一直在与时俱进，无数的新产品或新版本正在进入DevOps领域。当你有如此多的选择时，选择正确的CI/CD工具，就很有必要。

在所有可用的CI/CD工具中，你应该考虑的两个工具是Jenkins和GitLab CI/CD。

以[G2](https://www.g2.com/products/jenkins/reviews)为例，Jenkins在G2上的平均评分为4.3星，有288条评论，而GitLab CI/CD在[G2](https://www.g2.com/products/gitlab/reviews)上的评分为4.4星，有270条评论。Jenkins于2011年发布，属于CI/CD业务，它一直是测试人员的首选。然而，自2014年以来，GitLab CI/CD一直以其先进的功能受到越来越多人的注意。

作为DevOps测试专家，你需要根据项目，预算和其他要求仔细评估这些工具。为了帮助你，我将对[Jenkins vs GitLab CI/CD](https://www.lambdatest.com/blog/jenkins-vs-gitlab-ci-battle-of-ci-cd-tools/?utm_source=Dzone&utm_medium=blog&utm_campaign=RJ-140920-1&utm_term=RahulJain)进行深入评估，以帮助你选择适合你项目要求的CI/CD工具。

让我们从基础开始。





#### Jenkins

[Jenkins](https://www.jenkins.io/)是著名的可扩展的开源CI/CD工具，用于自动化部署。Jenkins完全用Java编写，并在MIT许可下启动。它具有一组强大的功能，可以自动执行与软件的构建，测试，部署，集成和发布有关的任务。可在macOS，Windows和各种UNIX版本（例如OpenSUSE，Ubuntu，Red Hat等）上使用。除了通过本机安装软件包进行安装外，它还可以独立安装或作为Docker在任何有Java Runtime Environment（JRE）的计算机上安装。

Jenkins团队还有一个名为Jenkins X的子项目，专门和Kubernetes一起使用。Jenkins X巧妙地集成了Helm，Jenkins CI/CD等工具。

使用Jenkins的一个优点是脚本结构合理，易于理解并且可读性强。Jenkins团队已经研发了大约1,000个插件，使该应用程序可以与其他熟悉的技术融合在一起。

此外，还可以使用Credentials Command插件，这使得在脚本中添加身份验证凭据等变得容易且可行。

Jenkins开始运行后，你还可以验证每个阶段是否通过或失败。但是，你无法在提供的图形界面中检查特定任务的状态。



##### Jenkins特点

Jenkins以易于设置，自动构建过程以及文档丰富而闻名。在进行DevOps时，Jenkins被认为是非常可靠的，并且可能不必监视整个构建过程，而其他CI/CD工具则并非如此。让我们看一下Jenkins提供的一些最重要的功能：

1. **免费，开源且易于安装**

   Jenkins可轻松用于macOS，Unix，Windows和平台。它可以与Docker结合使用，可以为自动化任务带来更高的一致性和效率。它也可以在Java容器（例如Apache Tomcat和GlassFish）中作为servlet运行。文档丰富，可以指导整个[安装过程](https://www.jenkins.io/doc/book/installing/)。

2. **丰富的插件生态系统**

   与其他CI/CD工具相比，Jenkins的插件生态系统更加成熟。目前，他们提供了1500多个插件。它使定制变得容易且有利可图。因此，你无需购买昂贵的插件。

3. **易于设置和配置**

   Jenkins设置和配置过程非常简单，安装过程仅需要一些步骤。Jenkins的升级过程也很轻松，直接。

4. **社区活跃**

   如你所知，这是一个具有丰富插件生态系统的开源项目，所有插件和功能都得到了社区的广泛支持。

5. **提供REST API**

   Jenkins提供了RESTful接口以实现可扩展性。具体参考，[Jenkins API文档](https://www.jenkins.io/doc/book/using/remote-access-api/)。

6. **并行执行**

   Jenkins支持并行。你可以轻松地将其与不同的工具集成，并在构建成功或失败时获得通知。开发人员甚至可以跨不同的虚拟机并行执行多个构建，从而提高效率。

7. **分布式任务**

   它可以毫不费力地运行分布式任务，即任务在不同的计算机上运行。



#### Gitlab CI/CD

在所有CI/CD工具中，[GitLab CI/CD](https://about.gitlab.com/)无疑是最新的一种。它是内置在GitLab CI/CD中的免费且自托管的持续集成工具。GitLab CI/CD具有社区版本，并提供git存储库管理，问题跟踪，代码审查，Wiki等。组织可以在本地安装GitLab CI/CD，并将其与Active Directory和LDAP服务器连接以进行安全授权和身份验证。

GitLab CI/CD以前是一个独立项目，于2015年9月与GitLab 8.0发行版集成。GitLab CI/CD提供了计划，打包，SCM，发布，配置和检查等功能。

GitLab CI/CD还提供了存储库，因此Gitlab CI/CD的集成非常简单明了。

在使用GitLab CI/CD时，包括一系列阶段，这些阶段将以精确的顺序执行。

每个任务都是一个阶段的一部分，并且将在一个阶段自动与其他任务并行运行。你可以在阶段内检查每个任务的状态。这就是GitLab CI/CD与其他CI/CD工具的不同之处。



##### Gitlab CI/CD特点	

1. **高可用性**
   GitLab CI/CD被广泛使用，并且是最新的开源CI/CD工具之一。GitLab CI/CD的安装和配置都很容易。它是内置在GitLab中的免费的自托管CI工具。

2. **Jekyll插件支持**
Jekyll插件是一个静态网站生成器，具有对GitHub Pages的强大支持，它使构建过程更简单。Jekyll插件支持使用HTML文件和Markdown并根据你的布局偏好创建一个完全静态的站点。通过编辑config.yml文件，可以轻松配置大多数Jekyll设置，例如，网站的插件和主题。

3. **里程碑设置**
   里程碑是跟踪问题，改进一系列问题的绝佳方法。

4. **可扩展**
   自动扩展缩放GitLab CI运行程序，可以轻松管理并节省90％的EC2成本。这是必不可少的，尤其是对于并行的测试环境。

5. **问题跟踪和问题修复**
   由于其出色的问题跟踪和问题修复功能，GitHub是众多开源项目首选的CI/CD工具。为了实现简单且无故障的监控，测试结果显示在GitHub UI上。

6. **使用访问控制管理Git存储库**
   你可以轻松地管理git存储库。你可以轻松地向单个存储库上的协作者授予写/读访问权限，对该组织的存储库具有更精细的访问控制。

7. **活跃的社区支持**
   活跃的社区是GitLab CI/CD的主要优点之一。所有支持都是开箱即用的，不需要在其他插件的安装中进行修改。

8. **代码审查和合并请求**
   GitLab CI/CD不仅用于构建代码，而且还用于检查或审查代码。它允许通过合并请求来改善协作。



#### 两者比较

Jenkins和GitLab CI/CD都擅长于自己的工作，并且拥有自己的技术支持。

|           特征           |                           Jenkins                            |                         GITLAB CI/CD                         |
| :----------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|        开源或商业        |                            开源的                            |                            开源的                            |
|         产品类别         |                       自托管/内部部署                        |                       自托管/内部部署                        |
|        内置CI/CD         |                       Jenkins支持CI/CD                       |          不需要为CI/CD安装任何东西，它具有内置功能           |
|        独特的功能        |                             插件                             |        AutoDevOps /允许将CI和代码管理保持在同一位置。        |
|         产品种类         |                         自托管/本地                          |                          SaaS /本地                          |
|         支持SLA          |                              否                              |                              是                              |
|        安装与安装        |                             简单                             |                             简单                             |
|        自托管选项        |               开源和自我托管是使用它的唯一方法               |                              是                              |
|         建立管道         |              通过Jenkins Pipeline DSL的定制管道              |                              是                              |
|     应用程序性能监控     |                     不提供分析性能的功能                     |              将显示所有已部署应用程序的性能指标              |
|         生态系统         |                       1000多个社区插件                       |                              是                              |
|        全面的API         |                     具有全面的API功能。                      |             提供用于软件项目中更深层集成的API。              |
| 特殊语言支持：JavaScript |                              是                              |                              是                              |
|         整合方式         |         允许与其他工具（例如：Slack，GitHub）集成。          |     可访问大量第三方集成，最著名的是GitHub和Kubernetes。     |
|     CI/CD部署仪表板      |                           部分支持                           | 每个用户可以根据流水线历史记录和项目中的最新状态，更改仪表板。 |
|           API            |                              是                              |              是的，提供了REST API和GraphQL API               |
|         代码质量         | 通过Sonarqube插件提供代码质量检查，另外还可以使用不同的插件来验证代码质量。 |              Gitlab还提供了检查代码质量的功能。              |















