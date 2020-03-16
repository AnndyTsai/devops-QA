# devops-QA

devops工程体系下的QA？

** devops、敏捷 ** 2020 QA应该做什么？持续集成似乎只是开发、运维的事情？测试能做什么？

* 对于测试环境，我想大致的流程应该是这样的吧！将质量技术手段整合在整个流程中，让测试不再是"测试"而是devops工程体系上的重要的环节，以技术手段赋能devops，推动开发提高质量，在敏捷模式下更好地保障质量赋能！

![ext1](https://github.com/AnndyTsai/devops-QA/blob/master/Pic/devops流程.png "QA与devops的整合-让敏捷更敏捷")

## 1：Jenkinsfile

* Jenkinsfile是一个比较基础的测试环境部署服务器的脚本文件，为什么是测试环境部署的脚本文件不用于生产环境呢？主要原因还是一般生产部署会将building好的安装包push到ftp服务器，最后远端服务器从ftp获取最新building的安装包进行安装部署

* 本文Jenkinsfile中描述的部署方式与传统的CI部署最大的不同是采用pipeline的方式进行部署，整合了sonarQube做代码的扫描和检测，同时可以触发自动化测试脚本（构建成功后悔自动触发自动化脚本的运行，构建失败则不会触发自动化脚本的执行），将开发、测试、运维整合在devops工程体系下，也是目前非常符合敏捷模式的一种devops的工程化实践
