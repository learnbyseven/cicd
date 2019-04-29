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
            openshift.withProject("p1") {
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
            openshift.withProject("p1") {
            openshift.tag("s2icode", "p2/s2icode:test")
          }
        }
      }
    }
  }
  stage('Deploy in TEST') {
     steps {
       script {
         openshift.withCluster() {
           openshift.withProject("p2") {
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
            openshift.withProject("p2") {
            openshift.tag("s2icode:test", "p3/s2icode:prod")
          }
        }
      }
    }
  }
  stage('Deploy in Production') {
     steps {
       script {
         openshift.withCluster() {
           openshift.withProject("p3") {
           openshift.newApp("s2icode:prod", "--name=s2icodeprod", "--allow-missing-images=true").narrow('svc').expose()
          }
        }
      }
    }
  }
 }
}
