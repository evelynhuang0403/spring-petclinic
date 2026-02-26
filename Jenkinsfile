pipeline {
    agent any

    triggers {
        // Every 5 minutes on Thursday
        cron('H/5 * * * 4')
    }

    tools {
        maven 'Maven'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out source code..."
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Compiling source code..."
                sh 'mvn -B clean compile'
            }
        }

        stage('Unit Test') {
            steps {
                echo "Running unit tests..."
                sh 'mvn -B test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Code Coverage (JaCoCo)') {
            steps {
                echo "Generating JaCoCo coverage report..."
                // generates target/site/jacoco/index.html
                sh 'mvn -B jacoco:report'
            }
        }

        stage('Publish Coverage') {
            steps {
                // Jenkins JaCoCo plugin publishing
                jacoco(
                    execPattern: 'target/jacoco.exec',
                    classPattern: 'target/classes',
                    sourcePattern: 'src/main/java'
                )
            }
        }

        stage('Package Artifact') {
            steps {
                echo "Packaging application..."
                sh 'mvn -B package -DskipTests'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        cleanup {
            cleanWs()
        }
    }
}
