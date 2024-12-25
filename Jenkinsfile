hpipeline {
  
  agent any

  environment {
    DOCKERHUB_CREDENTIALS = credentials('DOCKER_HUB_CREDENTIAL')
    VERSION = "${env.BUILD_ID}"
  }
  
  tools {
    maven "maven"
  }

  stages {

    //    stage('Custom---------------'){
    //     steps{
    //       sh 'ls ../../.ssh -a'
        
    //     }
    // }


    //    stage('Maven Build'){
    //     steps{
    //     sh 'mvn clean package  -DskipTests'
    //     }
    // }

    // stage('Maven Build'){
    //     steps{
    //       sh 'eval "$(ssh-agent -k)"'
    //       sh 'eval "$(ssh-agent -s)"'
    //       sh 'exec ssh-agent bash'
    //       sh 'ssh-add ../../.ssh/id_rsa'
    //     }
    // }


      stage('Docker Build and Push') {
      steps {
          // sh 'chmod -R 777 /var/run/docker.sock'
          sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
          sh 'echo $(docker -v)'
          // sh 'docker build -t vbmb2012/restaurantlisting:${VERSION} .'
          // sh 'docker push vbmb2012/restaurantlisting:${VERSION}'
      }
    } 

     stage('Cleanup Workspace') {
      steps {
        deleteDir()
      }
    }


    stage('Update Image Tag in GitOps') {
      steps {
         checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[ credentialsId: 'git-ssh', url: 'git@github.com:varunmzn/devops-deployment.git']])
        script {
       sh '''
          sed -i "s/image:.*/image: vbmb2012\\/restaurantlisting:${VERSION}/" local/restaurant-manifest.yml
        '''
          sh 'git checkout main'
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
  post {
        // Clean after build
        always {
           sh 'ls -a'
           sh 'chmod -R 777 .'
            cleanWs(cleanWhenNotBuilt: false,
                    deleteDirs: true,
                    disableDeferredWipeout: true,
                    notFailBuild: true,
                    // patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
                    //            [pattern: '.propsfile', type: 'EXCLUDE']]
                    )
        }
    }

}


