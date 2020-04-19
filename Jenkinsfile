#!groovy

@Library('jenkinslib-test') _
//加载library ,括号中内容是jenkins configuration中定义的名字, 引用一下是 下划线_
def tools = new org.devops.tools()
// 定义一个变量，来引用代码仓库中的/src/org/devops/tools.groovy，这里不用写src

String workspace = "/opt/jenkins/workspace"
hello()

//Pipeline
pipeline {
    agent { node {  label "master"   //指定运行节点的标签或者名称
                    customWorkspace "${workspace}"   //指定运行工作目录（可选）
            }
    }
    parameters { string(name: 'DEPLOY_ENV', defaultValue: 'staging', description: '') }
    
    options {
        timestamps()  //日志会有时间
        skipDefaultCheckout()  //删除隐式checkout scm语句
        disableConcurrentBuilds() //禁止并行
        timeout(time: 1, unit: 'HOURS')  //流水线超时设置1h
    }

    stages {

        //下载代码
        stage("GetCode"){ //阶段名称
            when { environment name: 'test', value: 'ABCD' }
            steps{  //步骤
                timeout(time:5, unit:"MINUTES"){   //步骤超时时间
                    script{ //填写运行代码
                        println('获取代码')
                        tools.PrintMes("获取代码",'green')
                        println("${test}")
                        //input id: 'Test', message: '我们是否要继续？', ok: '是，继续吧！', parameters: [choice(choices: ['a', 'b'], description: '', name: 'test1')], submitter: 'lizeyang,admin'
                        result = input id: 'Test', message: '我们是否要继续', ok: '是，继续吧', parameters: [choice(choices: ['a', 'b'], description: '', name: 'test1')], submitter: 'oscar ,admin'
                        println("${result}")
                        
                    }
                }
            }
        }

        stage("01"){
            failFast true
            parallel {
        
                //构建
                stage("Build"){
                    steps{
                        timeout(time:20, unit:"MINUTES"){
                            script{
                                println('应用打包')
                                tools.PrintMes("应用打包",'green')
                                mvnHome = tool "m2"
                                println(mvnHome)
                                
                                sh "${mvnHome}/bin/mvn --version"
                            }
                        }
                    }
                }
        
                //代码扫描
                stage("CodeScan"){
                    steps{
                        timeout(time:30, unit:"MINUTES"){
                            script{
                                print("代码扫描")
                                tools.PrintMes("代码扫描",'green')
                                tools.printMessage("This is my lib test!")
                            }
                        }
                    }
                }
            }
        }
    }

    //构建后操作
    post {
        always {
            script{
                println("always")
            }
        }

        success {
            script{
                currentBuild.description = "\n 构建成功!" 
            }
        }

        failure {
            script{
                currentBuild.description = "\n 构建失败!" 
            }
        }

        aborted {
            script{
                currentBuild.description = "\n 构建取消!" 
            }
        }
    }
}
