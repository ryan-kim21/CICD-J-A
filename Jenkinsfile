node {
    def app

    stage('Clone repository') {
      

        checkout scm
    }

    stage('Build image') {
  
       app = docker.build("ryankim5100/testjenkins") //dockerrepo
    }

    stage('Test image') {

        docker.install{
            sh '
            apt-get update
            apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release
            curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
            add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
            apt-get update
            apt-get install -y docker-ce docker-ce-cli containerd.io'
        }
        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {   //jenkins setting for docker hub
            app.push("${env.BUILD_NUMBER}")
        }
    }
    
    stage('Trigger ManifestUpdate') {
                echo "triggering updatemanifestjob"
                build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
        }
}