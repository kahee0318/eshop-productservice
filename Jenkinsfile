pipeline {
  agent {
    kubernetes {
      yaml """
kind: Pod
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: docker-config
        mountPath: /kaniko/.docker
  volumes:
    - name: docker-config
      projected:
        sources:
        configMap:
          name: docker-config
"""
    }
  }
  stages {
    
    stage('Approval') {
      when {
        branch 'main'
      }
      steps {
        script {
          def plan = 'productservice CI'
          input message: "Do you want to build and push?",
              parameters: [text(name: 'Plan', description: 'Please review the work', defaultValue: plan)]
        }
      } 
    }
        
    stage('Build with Kaniko') {
      steps {
        container(name: 'kaniko', shell: '/busybox/sh') {
          
          withCredentials([
          usernamePassword
            (credentialsId: 'github', 
             usernameVariable: 'USERNAME', 
             passwordVariable: 'GIT_TOKEN'
            )
          ])

          {
            sh '''#!/busybox/sh
            /kaniko/executor 
            --git branch=main 
            --context=git://$USERNAME:$GIT_TOKEN@github.com/kahee0318/eshop-productservice.git 
            --destination=307401367625.dkr.ecr.us-east-1.amazonaws.com/eshop-productservice:latest
            '''
          }
        }
      }
    }
  }

  post {
    success { 
      slackSend(channel: 'C040Z4P15GD', color: 'good', message: 'productservice CI success')
    }
    failure {
      slackSend(channel: 'C040Z4P15GD', color: 'danger', message: 'productservice CI fail')
    }
  }  
}