pipeline {
  environment {
    registry = "autocarmaua/docker-test"
    registryCredential = 'dockerhub'
    
    PATH = "$PATH:/usr/local/bin"
    
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git 'https://github.com/lastessa/jenkins_test.git'
      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    
    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }

    stage('Setting the variables values') {
    steps {
         sh '''
            #!/bin/bash
            echo "run docker with ftp"
            docker stop ftpd_server
            docker rm ftpd_server -f 
            docker run -d --name ftpd_server -p 21:21 -p 30000-30009:30000-30009 -e FTP_USER_NAME=denis -e FTP_USER_PASS=denis123 -e FTP_USER_HOME=/home/bob registry.hub.docker.com/autocarmaua/docker-test:13
            #rm -rf docker-compose.yml
            #wget https://raw.githubusercontent.com/lastessa/jenkins_test/master/docker-compose.yml
            #curl -L https://github.com/docker/compose/releases/download/1.8.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
            #chmod +x /usr/local/bin/docker-compose
            #ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
            
         '''
    }
}
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }

    stage('Upload') {
    steps {
        
          #  ftpPublisher alwaysPublishFromMaster: true, continueOnError: false, failOnError: false, publishers: [
          #      [configName: 'ftp-server', transfers: [
          #          [asciiMode: false, cleanRemote: false, excludes: '', flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: "$BUILD_NUMBER", remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**.exe, **.txt']
          #      ], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true]
          #  ]

            ftpPublisher alwaysPublishFromMaster: false, continueOnError: false, failOnError: false, publishers: [[configName: 'ftp-server', transfers: [[asciiMode: false, cleanRemote: false, excludes: '', flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'test', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**']], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false]]
        }
    }
  }
}