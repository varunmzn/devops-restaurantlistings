pipeline {
  
  agent any

  environment {
    DOCKERHUB_CREDENTIALS = credentials('DOCKER_HUB_CREDENTIAL')
    VERSION = "${env.BUILD_ID}"

  }

  tools {
    maven "maven"
  }

  stages {

    stage('Maven Build'){
        steps{
        sh 'ls'
        }
    }
    

    //   stage('Docker Build and Push') {
    //   steps {
    //       sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
    //       sh 'docker build -t vbmb2012/restaurantlisting:${VERSION} .'
    //       sh 'docker push vbmb2012/restaurantlisting:${VERSION}'
    //   }
    // } 

    //  stage('Cleanup Workspace') {
    //   steps {
    //     deleteDir()
    //   }
    // }




    stage('Update Image Tag in GitOps') {
      steps {
         checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[ credentialsId: 'git-ssh', url: 'git@github.com:varunmzn/devops-deployment.git']])
        script {
       sh '''
          sed -i "s/image:.*/image: vbmb2012\\/restaurantlisting:11/" local/restaurant-manifest.yml
        '''
          sh 'git checkout master'
          sh 'git add .'
          sh 'git commit -m "Update image tag"'
        sshagent(['git-ssh'])
            {
                  sh('git push')
            }
        }
      }
    }

  }

}


