pipeline {
    
    agent {
        label "linuxbuildnode"
    }
    
    
    stages {
        stage('SCM') {
            steps {
                git 'https://github.com/SUNILKUMARKUMAWAT371/jenkins-docker-maven-java-webapp1.git'
                
            }
            
        }
        
        stage('Build by Maven Package') {
            steps {
                sh 'mvn clean package'
            }
            
        }
        
        
        stage('Build Docker OWN image') {
            steps {
                sh "sudo docker build -t  sunilkumawat/javaweb:${BUILD_TAG}  ."
                //sh 'whoami'
            }
            
        }
        
        
        stage('Push Image to Docker HUB') {
            steps {
                
                withCredentials([string(credentialsId: 'DOCKER_HUB_PWD', variable: 'DOCKER_HUB_PASS_CODE')]) {
    // some block
                 sh "sudo docker login -u sunilkumawat -p $DOCKER_HUB_PASS_CODE"
}
               
               sh "sudo docker push sunilkumawat/javaweb:${BUILD_TAG}"
            }
            
        }
        
        
        stage('Deploy webAPP in DEV Env') {
            steps {
                sh 'sudo docker rm -f myjavaapp'
                sh "sudo docker run  -d  -p  8080:8080 --name myjavaapp   sunilkumawat/javaweb:${BUILD_TAG}"
                //sh 'whoami'
            }
            
        }
        
        
        stage('Deploy webAPP in QA/Test Env') {
            steps {
               
               sshagent(['QA_ENV_SSH_CRED']) {
    
                    sh "ssh  -o  StrictHostKeyChecking=no ec2-user@13.233.109.137 sudo docker rm -f myjavaapp"
                    sh "ssh ec2-user@13.233.109.137 sudo docker run  -d  -p  8080:8080 --name myjavaapp   sunilkumawat/javaweb:${BUILD_TAG}"
                }

            }
            
        }
        
        
         stage('QAT Test') {
            steps {
            retry(5) {
                sh 'curl --silent http://13.233.109.137:8080/java-web-app/ |  grep India'
               }
            }
        }
        
        /*
         stage('approved') {
            steps {
                input(message: "Release to Prod...?")
            }
        }
         */
         
         
        stage('approved') {
            steps {
                
            
            script {
                Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput

                if(userInput == true) {
                    // do action
                } else {
                    // not do action
                    echo "Action was aborted."
                }
            
                
            }
        }
        }
        
        
         
        
        stage('Deploy webAPP in Prod Env') {
            steps {
               
               sshagent(['QA_ENV_SSH_CRED']) {
    
                    sh "ssh  -o  StrictHostKeyChecking=no ec2-user@13.233.43.85 sudo docker rm -f myjavaapp"
                    sh "ssh ec2-user@13.233.43.85 sudo docker run  -d  -p  8080:8080 --name myjavaapp   sunilkumawat/javaweb:${BUILD_TAG}"
                }

            }
            
        } 
        
    
        
    }
    
    post {
        success {
            mail to: 'sunilkumarkumawat371@gmail.com',
            subject: "sucess Pipeline",
            body: "Something is very fantastic"
        }
    }
    
}
