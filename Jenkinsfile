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
                echo "========executing A========"
                sshagent (credentials: ['deploy-dev']) {
                sh "scp -o StrictHostKeyChecking=no target/*.war ubuntu@54.159.7.172:/prod/apache-tomcat-8.5.85/webapps/webapp.war"
                }
            }

        }
    }

}