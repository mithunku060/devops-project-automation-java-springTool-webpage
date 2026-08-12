pipeline{
    agent any
    stages{
        stage("code cloning"){
            steps{
                echo "Cloning the code"
                git credentialsId: 'gitHub', url: 'https://github.com/mithunku060/devops-project-automation-java-springTool-webpage'
            }
        }
        stage('maven test'){
            steps{
                dir("/var/lib/jenkins/workspace/project2"){
                    sh 'mvn test'
                }
            }
        }
        stage('maven build'){
            steps{
                dir("/var/lib/jenkins/workspace/project2"){
                    sh 'mvn clean install'
                }
            }
        }
        stage('maven integration test'){
            steps{
                dir("/var/lib/jenkins/workspace/project2"){
                    sh 'mvn verify'
                }
            }
        }
        stage("build"){
            steps{
                echo "docker image build"
                sh "docker build -t ci-cd-pipeline ."
            }
        }
        stage("Push to Docker Hub"){
            steps {
                echo "Pushing the image to docker hub"
                withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHub-pass', usernameVariable: 'dockerHub-user')]){
                sh "docker tag ci-cd-pipeline ${env.dockerHub-user}/ci-cd-pipeline:latest"
                sh "docker login -u ${env.dockerHub-user} -p ${env.dockerHub-pass}"
                sh "docker push ${env.dockerHub-user}/ci-cd-pipeline:latest"
                }
            }
        }
        stage("Deploy to kubernetes") {
            steps {
                script {
                    dir("notesapp") {
                        withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kuberneteslogin', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                        sh "kubectl delete --all pods"
                        sh "kubectl apply -f deployment.yaml"
                        sh "kubectl apply -f service.yaml"
                        }
                    }
                }
                
            }
        }
