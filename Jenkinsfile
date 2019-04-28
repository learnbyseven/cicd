pipeline {
        agent any 
}
stages {
  stage("BUILDAPP"){
    steps {
      scripts {
        openshift.withCluster() {
          openshift.withProject("dev") {
            openshift.newBuild("--name=vote", "--docker-image=docker.io/giriraj789/voteweb:v1", "--binary=true")
                }
            }
        }
    }
  }
}       
