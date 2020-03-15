//测试环境部署jenkins 实现开发-测试-运维一体化，践行devops工程化理念
pipeline{
	
	agent {
	    node{
           label "node1"  //label可以是node节点名称 也可以是node节点标签
           customWorkspace "/root/test" //指定工作空间
           }
	}

	stages{

		stage("获取Jenkins源码"){

			steps{
				echo '准备从github上克隆jenkins源码，由于clone比较慢，所有放弃这个步骤'
				//从github上拉取代码
				//git credentialsId: '84631f16-4000-4591-b342-068979628e', url: 'https://github.com/AnndyTsai/jenkins.git'
			}

		}

		stage("代码安全扫描"){

			steps{

				echo "开始进行代码安全扫描"

				dir("${workspace}/jenkins"){

					sh "mvn sonar:sonar -Dsonar.host.url=http://119.3.138.43:3389 -Dsonar.login=f192527af972925ea0a587b67a6a0d4a99186fa3 -Dsonar.projectKey=java -Dsonar.projectName=jenkinsDemo -Dsonar.sources=target/"
				}

			}
		}

		stage("构建Jenkins"){

			steps{
				echo '开始构建....'
				//进入工作目录
				sh "cd ${workspace}/jenkins/; \
					mvn clean install -Dmaven.test.skip=true"
			}

		}
		//前提是jenkins机器与远程服务器配置免密码登录
		stage("部署远端服务器"){ 
			steps{
                script{
                    //执行shell命令 获取执行结果
                    result = sh(script: "ssh root@119.3.138.43 netstat -anp|grep 8080|awk '{printf \$7}'|cut -d/ -f1", returnStdout: true).trim()
                    println '8080端口的PID是：'+result
                    //捕获异常，执行kill的时候可能会抛出异常
                    try{
                        
                        sh "ssh root@119.3.138.43 kill -9 ${result}"

                    }catch(exce){
                        
                        println "8080端口没有被占用"
                    }finally{
                    	//备份数据
                    	isExit = sh(script: "ls /root/server/tomcat/apache-tomcat-8.5.51", returnStatus: true)

                    	if(isExit == 0){ //文件存在则备份

                    		echo "备份tomcat文件为apache-tomcat-8.5.51-bak，删除上一次的备份文件"
                    		sh "ssh root@119.3.138.43 rm -rf /root/server/tomcat/apache-tomcat-8.5.51-bak"
                    		sh "ssh root@119.3.138.43 mv /root/server/tomcat/apache-tomcat-8.5.51 /root/server/tomcat/apache-tomcat-8.5.51-bak"

                    	}else{

                    		echo "路径/root/server/tomcat/apache-tomcat-8.5.51不存在"

                    	}
                    	dir = sh(script: "ssh root@119.3.138.43 pwd", returnStdout: true).trim()
						//远端服务器下载tomcat并解压
						sh "ssh root@119.3.138.43 mkdir -p /root/server/tomcat"
						sh "ssh root@119.3.138.43 rm -rf ${dir}/apache-tomcat-8.5.51.tar.gz"
                    	println '执行目录为：'+dir
						sh "ssh root@119.3.138.43 wget https://mirror.bit.edu.cn/apache/tomcat/tomcat-8/v8.5.51/bin/apache-tomcat-8.5.51.tar.gz"
						sh "ssh root@119.3.138.43 tar -xzvf ${dir}/apache-tomcat-8.5.51.tar.gz -C  /root/server/tomcat/"

						//copy jenkins war包到远端服务器tomcat webapps下面
						sh "scp -r ${workspace}/jenkins/war/target/*.war root@119.3.138.43:/root/server/tomcat/apache-tomcat-8.5.51/webapps/"

						//启动tomcat
						sh "ssh root@119.3.138.43 sh /root/server/tomcat/apache-tomcat-8.5.51/bin/startup.sh"

                    }
				}
			}
		}
	}
	//构建后的操作
	post{

		success{
		//构建成功 消息通知
		echo "building success"
		}
		failure{

		//构建失败消息通知
		echo "building failure"
		}
	}
}
