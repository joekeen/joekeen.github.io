---
layout: post
title: Read Maven POM Version in Jenkins Pipeline
categories: jenkins
---

Given the need to retrieve the version from the POM in a [scripted Jenkins pipeline](https://www.jenkins.io/doc/book/pipeline/syntax/#scripted-pipeline):

```groovy
node() {
    stage('Stage') {
        def version = (readFile('pom.xml') =~ '<version>(.+?)</version>')[1][1]
        
        // set build description
        currentBuild.description = "${BRANCH_NAME}, ${version}"
    }
}
```

Note: use `[1][1]` if you have a parent POM, otherwise `[0][1]`. 