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
            openshift.tag("dev/s2icode", "test/s2icode:test")
          }
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
           openshift.newApp("s2icode:test", "--name=web").narrow('svc').expose()
          }
        }
      }
    }
  }
}
}
