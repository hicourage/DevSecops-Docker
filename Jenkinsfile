pipeline {
    agent any
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "mvn_3.9.12"
    }

    stages {
        stage('Build') {
            steps {
                sh "mvn clean package -DskipTests=true"
                archive 'target/*.jar'
            }
        }
           stage('Test') {
            steps {
                sh "ls ; mvn test"
            }
            post{
                always {
                    junit 'target/surefire-reports/*.xml'
                    jacoco (execPattern: 'target/jacoco.exec')
                  }
             }
        }
           stage('Docker Build and Push') {
            steps {
              withDockerRegistry(credentialsId: 'docker-hub', url: 'https://hub.docker.com/')  {
                sh 'printenv'
                sh 'docker build -t hub.docker.com/couragethedog/numeric-app:""$GIT_COMMIT"" .'
                sh 'docker push hub.docker.com/couragethedog/numeric-app:""$GIT_COMMIT""'
              sh "echo 'build and push to repository'"
            }
         }
      }
   }   
}
