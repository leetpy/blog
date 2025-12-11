---
title: Jenkins
date: 2019-02-11T14:46:20+08:00
lastmod: 2025-12-11T14:46:20+08:00
author: 云弋
# avatar: /img/author.jpg
# authorlink: https://author.site
cover: /blog/img/jenkins.png
# covercaption: a description of the cover image
# images:
#   - /img/cover.jpg
categories:
  - CI/CD
tags:
  - jenkins
# nolastmod: true
# math: true
draft: false
---

jenkins 常规配置和 pipeline 语法

<!--more-->

## 查看全局变量

`<ur.>/env-vars.html/`

## pipeline 语法

完整的文档参考： https://www.jenkins.io/zh/doc/

### 学习地址

访问： <jenkins_url>/job/testp/pipeline-syntax/ 可以利用 jenkins 界面生成相关的 pipeline 脚本。

### 语法

#### 使用变量

```groovy
pipeline {
    environment {
        APP_NAME = "my_app"
    }

    stages {
        stage('test_env') {
            steps {
                sh '''
                echo ${APP_NAME}
                '''
            }
        }
    }
}
```

#### 触发其它 job

```groovy
// 等待
 stage("step1") {
    steps {
         build job: 'job1', parameters: []
     }
 }

// 加参数，不等待
stage("step1") {
      steps {
          build job: 'job1', parameters: [string(name: 'Name', value: 'Baz2')], wait: false
      }
 }

 // 串行触发多个 job
 stage("step1") {
      steps {
          build job: 'job1', parameters: []
          build job: 'job2', parameters: []
      }
 }

 // 并行触发多个 job，并等待完成
 stage('step1') {
    def jobs = [:]
    jobs[0] = {build job: 'job1', parameters: [string(name: 'Name', value: param)], quietPeriod: 2}
    jobs[1] = {build job: 'job2', parameters: [string(name: 'Name', value: param)], quietPeriod: 2}
    parallel jobs
 }

// 调用其它 job 时，传递自身参数
pipeline {
    agent {
        label "slave"
    }

    parameters {
        string(name: 'version', defaultValue:'', description: 'version name, eg: v5.2')
    }

    stages {
        stage("trigger jobs") {
            steps {
                build job: 'child-job', parameters: [string(name: 'version', value: params.version)]
            }
        }
    }
}
```
