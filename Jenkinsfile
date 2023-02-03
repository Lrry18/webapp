pipeline{

    agent any

    tools{
        maven 'Maven'
    }

    stages{

        stage("Initialize"){
            steps{
                 sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                '''
            }
        }

        stage("Build"){
            steps{
                sh 'mvn clean package'
            }
        }

        stage("Deploy-to-Tomcat"){
            steps{
                sshagent (credentials: ['tomcat']) {
                sh "scp -o StrictHostKeyChecking=no target/*.war ubuntu@54.159.7.172:/prod/apache-tomcat-8.5.85/webapps/webapp.war"
                }
            }

        }

        step("Check-Git-Secrets"){
            steps{
                sh 'rm trufflehog || true '
                sh 'docker run  gesellix/trufflehog --json https://github.com/cehkunal/webapp.git > trufflehog'
                sh 'cat trufflehog'
            }
        }


    }

}