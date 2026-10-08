pipeline {
    agent any

    environment {
        IMAGE_BACK  = 'maramdevops_backend'
        IMAGE_FRONT = 'maramdevops_front'
    }

    stages {
        stage('Maven Clean') {
            steps {
                dir('backend') {
                    sh 'mvn clean'
                }
            }
        }

        stage('Maven Package') {
            steps {
                dir('backend') {
                    sh 'mvn install package -DskipTests'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('backend') {
                    withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                        sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:5.1.0.4751:sonar -Dsonar.projectKey=gestion-projets -Dsonar.host.url=http://localhost:9000 -Dsonar.login=$SONAR_TOKEN'
                    }
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker compose build'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
                    sh '''
                        echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                        docker tag $IMAGE_BACK  $DH_USER/$IMAGE_BACK:latest
                        docker tag $IMAGE_FRONT $DH_USER/$IMAGE_FRONT:latest
                        docker push $DH_USER/$IMAGE_BACK:latest
                        docker push $DH_USER/$IMAGE_FRONT:latest
                    '''
                }
            }
        }

        stage('Docker Deploy') {
            steps {
                sh 'docker compose up -d'
            }
        }
    }
}
