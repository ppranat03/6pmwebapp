/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any
    environment {
        PATH = "${PATH}:${tool name: 'maven3', type: 'maven'}/bin"
    }

    stages {
        stage('SCM Checkout') {
            steps {
                git credentialsId: 'github',
                    url: 'https://github.com/ppranat03/6pmwebapp',
                    branch: 'master'
            }
        }
        stage('Maven build') {
            steps {
                script {
                    sh  script: 'mvn  clean package'
                }
            }
        }
        stage('Deploy Dev') {
            steps {
                sshagent(['tomcat-dev']) {
                    // stop tomcat
                    sh 'ssh ec2-user@172.31.8.115 /opt/tomcat8/bin/shutdown.sh'
                    // copy war file to remote tomcat
                    sh 'scp target/6pmwebapp.war ec2-user@172.31.8.115:/opt/tomcat8/webapps/'
                    // start tomcat
                    sh 'ssh ec2-user@172.31.8.115 /opt/tomcat8/bin/startup.sh'
                }
            }
        }
    }
    post {
        success {
            echo 'sending email about build success'
            mail bcc: '', body: 'Successfully deployed Jenkins', cc: '', from: '', replyTo: '', subject: 'Successfully deployed', to: 'ppranat03@gmail.com'
        }
    }
}
