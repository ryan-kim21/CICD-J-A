podTemplate(
    label: 'mypod',
    volumes: [
        emptyDirVolume(mountPath: '/etc/gitrepo', memory: false),
        hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
    ],
    containers:
    [
        containerTemplate(name: 'git', image: 'alpine/git', ttyEnabled: true, command: 'cat'),
        containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true,
            envVars: [secretEnvVar(key: 'DOCKER_HUB_PASSWORD', secretName: 'docker-hub-password', secretKey: 'DOCKER_HUB_PASSWORD')]
        ),
        containerTemplate(name: 'node', image: 'node:10-alpine', command: 'cat', ttyEnabled: true)
    ]
)
{
node {
    def app

    stage('Clone repository') {
      

        checkout scm
    }

    stage('Build image') {
       sh 'docker build -t my-image .'
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
}