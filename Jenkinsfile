pipeline {
   agent none
   stages {
      stage('Source Code') {
         agent { label ' k8s-slave' }
         steps {
            git 'https://github.com/balajikubenthiran21031989/DevOpsProfessional.git'
         }
      }
      stage('Dockerize-app') {
         agent { label 'k8s-slave' }
         steps {
            sh 'docker build /home/ubuntu/jenkins/workspace/Dockerize-app/ -t kkbalajius/devops-capstone-project1'
            sh 'docker tag kkbalajius/devops-capstone-project1 kkbalajius/devops-capstone-project1'
            sh 'docker login -u "kkbalajius" -p "Kingwith8@l@j!" docker.io'
            sh 'docker push kkbalajius/devops-capstone-project1'
            echo 'Dockerise the production app....'
         }
      }
      stage('Run container') {
         agent { label 'k8s-slave' }
         steps {
            sh 'docker rm -f $(sudo docker ps -a -q)'
            sh 'docker run -d -p 85:80 --name=devops-capstone-project1 kkbalajius/devops-capstone-project1'
         }
      }
      stage('Website test') {
         agent { label ' k8s-slave' }
         steps {
            sh "hostname -I"
            sh "curl -I localhost:85"
            sh "ansible-playbook website-checker.yaml"
            echo 'Website is running fine on 85 port....'
         }
      }
      stage('clean test server') {
         agent { label 'k8s-slave' }
         steps {
            sh "ansible-playbook clean-docker.yaml"
         }
      }
      stage('Deploy to prod server') {
         agent { label 'k8s-master' }
         steps {
                    git 'https://github.com/balajikubenthiran21031989/DevOpsProfessional.git'
                    sh "kubectl delete -f deployment.yaml"
                    sh "kubectl delete -f service.yaml"
                    sh "docker rm -f \$(sudo docker ps -a -q)"
                    sh "docker rmi -f \$(docker images -q)"
                    sh "kubectl apply -f deployment.yaml"
                    sh "kubectl apply -f service.yaml"
                }
            }
        }
   }

