pipeline {
  agent any

  tools {
    jdk "jdk17"
    maven "M3"
  }
  
  stages {
      stage('Git clone'){
        steps {
            echo 'Git clone'
            git url: 'https://github.com/keg1213/spring-petclinic.git',
              branch: 'efficient-webjars'
        }
        post {
          success {
            echo 'Git clone Success!!'
          }
          failure {
            echo 'Git clone Fail'
          }
        }
      }  
      stage('Maven Build'){
        steps {
            echo 'Maven Build'
            sh 'mvn -Dmaven.test.failure.ignore=true clean package'
        }
        post {
            success {
            junit 'target/surefire-reports/**/*.xml'
            }
        }
      }
      stage('SSH Publish'){
        steps {
            echo 'SSH Publish'
            sshPublisher(publishers: [sshPublisherDesc(configName: 'target', 
                transfers: [sshTransfer(cleanRemote: false, 
                excludes: '', 
                execCommand: '''
                fuser -k 8080/tcp
                export BUILD_ID=Pipeline-Test
                nohup java -jar /home/ubuntu/deploy/spring-petclinic-2.7.3.jar >> nohup.out 2>&1 &''', 
                execTimeout: 120000, 
                flatten: false, 
                makeEmptyDirs: false, 
                noDefaultExcludes: false, 
                patternSeparator: '[, ]+', 
                remoteDirectory: 'deploy', 
                remoteDirectorySDF: false, 
                removePrefix: 'target', 
                sourceFiles: 'target/*.jar')], 
                usePromotionTimestamp: false, 
                useWorkspaceInPromotion: false, verbose: false)])
        }
      }
    }
}