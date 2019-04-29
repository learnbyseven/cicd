pipeline {
  agent any
  stages {
    stage('Build Application') {
      when {
        expression {
          openshift.withCluster() {
            return !openshift.selector('bc', 'web').exists();
          }
        }
      }
      steps {
        script {
          openshift.withCluster() {
            openshift.withProject("dev") {
            openshift.newApp('openshift/webbuilder:s2i~https://github.com/learnbyseven/s2icode.git --allow-missing-images --strategy=source')
          }
        }
      }
    }
  }
   stage('Promote to TEST') {
      steps {
        script {
          openshift.withCluster() {
            openshift.withProject("dev") {
            openshift.tag("s2icode", "test/s2icode:test")
          }
        }
      }
    }
  }
  stage('Deploy in TEST') {
     steps {
       script {
         openshift.withCluster() {
           openshift.withProject("test") {
           openshift.newApp("s2icode:test", "--name=s2icodetest", "--allow-missing-images=true").narrow('svc').expose()
          }
        }
      }
    }
  }
}
  stage('Promote to Production') {
      steps {
        script {
          openshift.withCluster() {
            openshift.withProject("test") {
            openshift.tag("s2icode:test", "prod/s2icode:prod")
          }
        }
      }
    }
}
  stage('Deploy in Production') {
     steps {
       script {
         openshift.withCluster() {
           openshift.withProject("prod") {
           openshift.newApp("s2icode:prod", "--name=s2icodeprod", "--allow-missing-images=true").narrow('svc').expose()
          }
        }
      }
    }
  }
}
}
