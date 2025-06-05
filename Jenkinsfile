
pipeline {
    agent any 
    stages {
        stage('Code Quality') { 
            steps {
                echo 'Sonar Analysis Started'
                sh 'cd webapp && sudo docker run --rm -r SONAR_HOST_URL="http://52.203.215.109:9000/" -v ".:/usr/src" -e SONAR_TOKEN="sqp_fcc99b940e63b0fa9a9d7f74da9d71411fadadeb" sonarsource/sonar-scanner-cli -Dsonar.projectkey=lms'
                echo 'Sonar Analysis Completed'
            }
        }
        stage('Build') { 
            steps {
                echo 'LMS Build Started'
                sh 'cd webapp && npm install && npm run build'
                echo 'Lms Build Completed'
            }
        }
        stage('Publish LMS NEXUS ') {  
            steps {
                script {
                    def packageJson = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJson.version
                    echo"${packageJSONVersion}"
                    sh "zip webapp/lms-${packageJSONVersion}.zip -r webapp/dist"
                    sh "curl -v -u admin:lms12345 --upload-file webapp/lms-${packageJSONVersion}.zip http://52.203.215.109:8081/repository/lms/"
            }
        }
    }

         stage('Deploy LMS') {
             steps {
                script {
                def packageJson = readJSON file: 'webapp/package.json'
                def packageJSONVersion = packageJson.version
                echo"${packageJSONVersion}"
                sh "curl -u admin:ravi@2427 -X GET \'http://52.203.215.109:8081/repository/lms/lms-${packageJSONVersion}.zip\' --output lms-'${packageJSONVersion}'.zip"
                sh 'sudo rm -rf /var/www/html/*'
                sh "sudo unzip -o lms-'${packageJSONVersion}'.zip"
                sh "sudo cp -r webapp/dist/* /var/www/html"
                }

            }
        }

            stage ('clean up wrokspace') {
                steps { 
                echo  'clening work space'
                // Install cleaning wrokspace plugin to make below command work
                cleanWs()

            }
                
            }
        }
}