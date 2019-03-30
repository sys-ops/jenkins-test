pipeline {
    agent any
    environment {
        // Docker Hub username/app name
        DOCKER_IMAGE_NAME = "andrzejewskidan/train-schedule"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_andrzejewskidan') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Continous Delivery Not Ready Yet : Deploy to Production?'
                milestone(1)
                //implement Kubernetes deployment here
                kubernetesDeploy(
                  kubeconfigId: 'kubeconfig',
                  configs: 'test-kubernetes-continous-deploy.yaml',
                  enableConfigSubstitution: true
                )
            }
        }
    }
}
