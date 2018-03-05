pipeline {
    agent any

    parameters {
         string(name: 'tomcat_dev', defaultValue: '18.218.41.177', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '18.221.65.150', description: 'Production Server')
    }

    environment{
        PATH = "/Applications/XAMPP/htdocs/apache-maven-3.5.2/bin/:$PATH"
    }

    triggers {
         pollSCM('* * * * *')
     }

stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no -i /Users/naamacohen/itc/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no -i /Users/naamacohen/itc/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}