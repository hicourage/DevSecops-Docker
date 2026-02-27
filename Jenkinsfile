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
      //      stage('Docker Build and Push') {
      //       steps {
      //         withDockerRegistry(credentialsId: 'docker-hub', url: 'https://docker.io/')  {
      //           sh 'printenv'
      //           sh 'docker build -t docker.io/couragethedog/numeric-app:""$GIT_COMMIT"" .'
      //           sh 'docker push docker.io/couragethedog/numeric-app:""$GIT_COMMIT""'
      //         sh "echo 'build and push to repository'"
      //       }
      //    }
      // }
        stage('Docker Build and Push') {
            steps {
                // Ensure credentialsId matches the ID in Jenkins Global Credentials
                withDockerRegistry(credentialsId: 'docker-hub', url: 'https://index.docker.io') {
                    
                    // 1. Build the image using the Git Commit hash as the tag
                    // Using ${GIT_COMMIT} inside double quotes for proper interpolation
                    sh "docker build -t couragethedog/numeric-app:${GIT_COMMIT} ."
                    
                    // 2. Push the specific tag to Docker Hub
                    sh "docker push couragethedog/numeric-app:${GIT_COMMIT}"
                    
                    sh "echo 'Build and push to repository completed for commit: ${GIT_COMMIT}'"
                }
            }
        }
   }   
}
