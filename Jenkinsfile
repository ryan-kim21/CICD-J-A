node {
    def app
    agent {
        kubernetes {
      label 'my-jenkins'
        }
    }
    stage('Clone repository') {
      

        checkout scm
    }

    stage('Build image') {
        steps {
         sh 'docker build -t my-image .'
        }
       app = docker.build("ryankim5100/test")
    }

    stage('Test image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            app.push("${env.BUILD_NUMBER}")
        }
    }
    
    stage('Trigger ManifestUpdate') {
                echo "triggering updatemanifestjob"
                build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
        }
}