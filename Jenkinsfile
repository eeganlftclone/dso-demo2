pipeline {
  agent {
    kubernetes {
      yamlFile 'build-agent.yaml'
      defaultContainer 'maven'
      idleMinutes 1
    }
  }
  stages {
    stage('Build') {
      parallel {
        stage('Compile') {
          steps {
            container('maven') {
              sh 'mvn compile'
            }
          }
        }
      }
    }
    stage('Test') {
      parallel {

      stage('SAST') {
        
          steps {
            
            container('slscan') {
              sh 'scan --type java,depscan --build'
            }
            
          }
        
          post {
            success {
              archiveArtifacts allowEmptyArchive: true, artifacts: 'reports/*', fingerprint: true, onlyIfSuccessful: true
            }
          }
        
        }



        
        stage('Unit Tests') {
          steps {
            container('maven') {
              sh 'mvn test'
            }
          }
        }
      }
    }
    stage('Package') {
      parallel {
        stage('Create Jarfile') {
          steps {
            container('maven') {
              sh 'mvn package -DskipTests'
            }
          }
        }

stage('OCI Image BnP') {
     steps {
       container('kaniko') {
         sh '/kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --cache=true --destination=docker.io/eeganlf/dsodemo'
          }
        }
      } 
        
    }
  }

    stage('Deploy to Dev') {
      steps {
        // TODO
        sh "echo done"
      }
    }
  }
}
