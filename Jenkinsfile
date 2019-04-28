pipeline {
  agent any
  stages {
    stage('Build') {
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
}
stage('Promote to TEST') {
      steps {
        script {
          openshift.withCluster() {
            openshift.withProject("dev") {
            openshift.tag("docker-registry.default.svc:5000/dev/s2icode:latest", "docker-registry.default.svc:5000/test/s2icode:test")
          }
        }
      }
    }
  }
}
