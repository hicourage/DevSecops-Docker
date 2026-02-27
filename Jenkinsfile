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
                // withDockerRegistry handles login automatically using your credentialsId
                withDockerRegistry(credentialsId: 'docker-hub', url: 'https://index.docker.io/v1/') {
                    
                    // 1. Build and tag with Git Commit
                    sh "docker build -t couragethedog/numeric-app:${GIT_COMMIT} ."
                    
                    // 2. Add the 'latest' tag to the image we just built
                    sh "docker tag couragethedog/numeric-app:${GIT_COMMIT} couragethedog/numeric-app:latest"
                    
                    // 3. Push both tags to Docker Hub
                    sh "docker push couragethedog/numeric-app:${GIT_COMMIT}"
                    sh "docker push couragethedog/numeric-app:latest"
                    
                    sh "echo 'Successfully pushed tags: ${GIT_COMMIT} and latest'"
                }
            }
        }

   }   
}
