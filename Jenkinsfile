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

        stage("Check-Git-Secrets"){
            steps{
                sh 'rm trufflehog || true '
                sh 'docker run  gesellix/trufflehog --json https://github.com/cehkunal/webapp.git > trufflehog'
                sh 'cat trufflehog'
            }
        }

        stage("Source Compilation Analysis"){
            steps{
                sh 'rn owasp* || true'
                sh 'wget https://raw.githubusercontent.com/Lrry18/webapp/main/owasp-dependency-check.sh'
                sh 'chmod +x owasp-dependency-check.sh'
                sh 'bash owasp-dependency-check.sh'
                sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
            }
        }

        stage("SAST"){
            steps{
                withSonarQubeEnv('sonar'){
                    sh 'mvn sonar:sonar'
                    sh 'cat target/sonar/report-task.txt'
                }
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

        stage("DAST"){
            steps{
                sshagent(['zap']){
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@54.167.57.209 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://54.159.7.172:8080/webapp" '
                }
            }
        }

        


    }

}