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
            /kaniko/executor \\
            --git branch=main \\
            --context=git://$USERNAME:$GIT_TOKEN@github.com/kahee0318/eshop-MSA.git \\
            --context-sub-path=eshop-productservice \\
            --destination=307401367625.dkr.ecr.us-east-1.amazonaws.com/eshop-productservice:latest
            '''
          }
        }
      }
    }
  }  
}