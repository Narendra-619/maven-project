pipeline {
    agent {
        label 'DevServer'
    }

    parameters {
        choice(
            name: 'select_environment',
            choices: ['dev', 'prod'],
            description: 'Select deployment environment'
        )
    }

    environment {
        NAME = "Messi"
    }

    tools {
        maven 'mymaven'
    }

    stages {

        stage('build') {
            steps {
                echo "Building application..."
                sh 'mvn clean package -DskipTests=true'
            }
        }

        stage('test') {
            parallel {

                stage('testA') {
                    agent { label 'DevServer' }
                    steps {
                        echo "Running Test A"
                        sh 'mvn test'
                    }
                }

                stage('testB') {
                    agent { label 'DevServer' }
                    steps {
                        echo "Running Test B"
                        sh 'mvn test'
                    }
                }
            }

            post {
                success {
                    dir('target') {
                        stash name: 'maven-build', includes: '*.jar'
                    }
                }
            }
        }

        stage('deploy_dev') {
            when {
                expression { params.select_environment == 'dev' }
                beforeAgent true
            }

            agent {
                label 'DevServer'
            }

            steps {
                echo "Deploying to Dev Environment"

                dir('/home/ubuntu/deploy') {
                    unstash 'maven-build'

                    sh '''
                    ls -l
                    java -jar my-app-1.0-SNAPSHOT.jar &
                    '''
                }
            }
        }
    }
}