pipeline {
    agent {
        label 'DevServer'
    }

    parameters {
        choice(
            choices: ['dev', 'prod'],
            name: 'select_environment'
        )
    }

    tools {
        maven 'mymaven'
    }

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }

        stage('Test') {
            parallel {

                stage('TestA') {
                    steps {
                        echo "Running Test A"
                        sh 'mvn test'
                    }
                }

                stage('TestB') {
                    steps {
                        echo "Running Test B"
                        sh 'mvn test'
                    }
                }
            }

            post {
                success {
                    dir('webapp/target') {
                        stash name: 'maven-build', includes: '*.war'
                    }
                }
            }
        }

        stage('Deploy Dev') {
            when {
                expression { params.select_environment == 'dev' }
            }

            steps {
                dir('/home/ubuntu/deploy') {
                    unstash 'maven-build'

                    sh '''
                        rm -rf *
                        jar -xvf *.war
                    '''
                }
            }
        }
    }
}