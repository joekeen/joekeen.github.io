---
layout: post
title: Parameter pool in Jenkins Pipeline
categories: jenkins
---

Given the need for [scripted Jenkins pipeline](https://www.jenkins.io/doc/book/pipeline/syntax/#scripted-pipeline) jobs to choose a resource from a finite set, we can use the following in our Jenkinsfile to choose an available resource.

```groovy
import org.jenkins.plugins.lockableresources.LockableResourcesManager as manager

def getResource(resources) {
    return resources.find { 
        resource -> manager.get().fromName(resource) == null ? resource : 
                manager.get().fromName(resource).with { r -> !r.isLocked() && !r.isQueued() } }
}

node() {
    stage('Deploy') {
        def database = null
        waitUntil {
            database = getResource(["DB1", "DB2", "DB3", "DB4"])
        }
        ...
        lock(database) {
            ...
        }
    }
}
```

The [Parameter Pool](https://plugins.jenkins.io/parameter-pool/) plugin does appear to be compatible with Jenkins pipelines, so this is solution for pipelines.