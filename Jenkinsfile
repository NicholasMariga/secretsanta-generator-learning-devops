

pipeline {
    //agent any
    //add slave nodes here
    agent {label 'slave-1'}
    
    tools {
        maven 'maven'
        jdk 'jdk17'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        //stage('Git Checkout') {
        //    steps {
        //        git 'https://github.com/NicholasMariga/secretsanta-generator-learning-devops.git'
        //   }
        //}
        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }
        stage('Tests') {
            steps {
                sh "mvn test"
            }
        }
        stage('Sonaqube Analysis') {
            steps {
                //name of the sonaqube server set in system under manage jenkins
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=santa \
                     -Dsonar.projectKey=santa -Dsonar.java.binaries=. ''' 
                }
            }
        }
        //stage('OWASP Scan') {
        //    steps {
        //        dependencyCheck additionalArguments: ' --scan .', odcInstallation: 'DC'
        //        dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        //    }
        //}
        stage('Build Application') {
            steps {
                sh "mvn package"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'Docker-1'){
                        sh "docker build -t santa:latest ."
                    }
                }
            }
        }
        stage('Tag & Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'Docker-1'){
                        sh "docker tag santa:latest marigah/santa:latest"
                        sh "docker push marigah/santa:latest"
                    }
                }
            }
        }
        stage('Depoy Application') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'Docker-1'){
                        sh "docker run -d -p 8081:8080 marigah/santa:latest"
                    }
                }
            }
        }
    }
    
    
//Email notification
post {
    success {
        emailext (
            subject: "Successful Build: ${env.JOB_NAME}",
            body: """<html>
                        <body>
                            <p>Build Name: ${env.JOB_NAME}</p>
                            <p>Build Number: ${env.BUILD_NUMBER}</p>
                            <p>Check the <a href="${env.BUILD_URL}">console output</a>.</p>
                        </body>
                    </html>""",
            attachLog: true,
            to: 'nicholasmarigah@gmail.com',
            from: 'awsmariga@gmail.com',
            replyTo: 'awsmariga@gmail.com',
            mimeType: 'text/html'
        )
    }

    failure {
        emailext (
            subject: "Failed Build: ${env.JOB_NAME}",
            body: """<html>
                        <body>
                            <p>Build Name: ${env.JOB_NAME}</p>
                            <p>Build Number: ${env.BUILD_NUMBER}</p>
                            <p>Check the <a href="${env.BUILD_URL}">console output</a>.</p>
                        </body>
                    </html>""",
            attachLog: true,
            to: 'nicholasmarigah@gmail.com',
            from: 'awsmariga@gmail.com',
            replyTo: 'awsmariga@gmail.com',
            mimeType: 'text/html'
        )
    }
    aborted {
        emailext (
            subject: "Aborted Build: ${env.JOB_NAME}",
            body: """<html>
                        <body>
                            <p>Build Name: ${env.JOB_NAME}</p>
                            <p>Build Number: ${env.BUILD_NUMBER}</p>
                            <p>Check the <a href="${env.BUILD_URL}">console output</a>.</p>
                        </body>
                    </html>""",
            attachLog: true,
            to: 'nicholasmarigah@gmail.com',
            from: 'awsmariga@gmail.com',
            replyTo: 'awsmariga@gmail.com',
            mimeType: 'text/html'
        )
    }
 }
}
