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
   stage("wait_prior_starting_smoke_testing") {
     steps {
       script {
         echo "Waiting 120 seconds for deployment to complete prior starting smoke testing" {
           sleep(time:120,unit:"SECONDS") {
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
            openshift.tag("dev/s2icode:latest", "test/s2icode:test")
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
  stage('Promote to Production') {
      steps {
        script {
          openshift.withCluster() {
            openshift.withProject("dev") {
            openshift.tag("dev/s2icode:latest", "prod/s2icode:prod")
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
