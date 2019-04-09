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

stage("Gather Deployment Parameters") {
            steps {
                timeout(time: 30, unit: 'SECONDS') {
                    script {
                        // Show the select input modal
                       def INPUT_PARAMS = input message: 'Please Provide Parameters', ok: 'Next',
                                        parameters: [
                                        choice(name: 'SUBFOLDER', choices: ['test1','test2'].join('\n'), description: 'Please select the subfolder'),
                                        string(defaultValue: '20190416', description: 'Choose date of build', name: 'DATE')]
                        env.SUBFOLDER = INPUT_PARAMS.SUBFOLDER
                        env.DATE = INPUT_PARAMS.DATE
                    }
                }
            }
        }


    stage('Building image') {
      steps{
        script {
        echo "Selected Folder: ${env.SUBFOLDER}"
        echo "Selected DATE: ${env.DATE}"
          dockerImage = docker.build registry + ":${env.DATE}"
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
        sh "docker stop ftpd_server"
        sh "docker rm ftpd_server -f"
        echo "RUN DOCKER Selected Folder: ${env.SUBFOLDER}"
        sh "echo date and subfolder ${env.SUBFOLDER} " 
        sh "docker run -d --name ftpd_server -p 21:21 -p 30000-30009:30000-30009 -e FTP_USER_NAME=denis -e FTP_USER_PASS=denis123 -e FTP_USER_HOME=/home/bob registry.hub.docker.com/autocarmaua/docker-test:${env.DATE}"
        
    }
}
    //stage('Remove Unused docker image') {
    //  steps{
    //    sh "docker rmi $registry:${env.DATE}"
    //  }
   // }

    stage('Upload to FTP') {
    steps {
        //sh '''
        //    #!/bin/sh
        //    echo "upload file to remote ftp ${env.SUBFOLDER}"
        //    lftp denis:denis123@192.168.254.162 -e "mirror -R ${env.SUBFOLDER} ${env.SUBFOLDER}; bye"
        //'''
        //sh "lftp denis:denis123@192.168.254.162 -e "mirror -R ${env.SUBFOLDER} ${env.SUBFOLDER}; bye" "
        
        sh '''
        lftp denis:denis123@192.168.254.162 -e "mirror -R ${env.SUBFOLDER} ${env.SUBFOLDER}; bye"
        ''' 

        }
    }
  }
}