pipeline {
    agent any
    stages {
        stage {'SCM checkout'} {
            steps {
                script{
                     git credentialsId: 'git-token', url: 'https://github.com/Faris2604/ecs-proj.git'
                }
            }
        }
        stage ('SonarQube Code analysis'){
            steps {
                script{
                    def scannerHome = tool 'sonarscanner4';
                    withSonarQubeEnv('sonar-pro') {
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=ui-reactjs"
                    }
                }
            }
        }
        stage ('Build') {
            steps {
                script{
                     sh 'npm install'
                }
            }
        }
        stage ('Docker Build Images') {
            steps {
                script {
                    sh 'docker build -t farish2604/helm:v1 .'
                    sh 'docker images'
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
                        sh "docker login -u faris2604 -p ${dockerPassword}"
                        sh 'docker push faris2604/helm:v1'
                    }
                }
            }
        }
        stage('Deploy on k8s') {
            steps {
                script {
                    withKuberCredentials(kubectlCredentials: [[ credentialsId: 'kubernetes']]) {
                        sh 'kubectl create secret generic helm --from-file=.dockerconfigjson=/opt/docker/config.json  --type kubernetes.io/
                        sh 'kubectl apply -f secret.yaml'
                        sh 'helm package ./Helm'
                        sh 'helm install myrocket ./myrocketapp-0.1.0.tgz'
                        sh 'helm ls'
                        sh 'kubectl get pods -o wide'
                        sh 'kubectl get svc'
                        

                    }
                }
            }
        }



}
