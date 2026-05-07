```groovy
pipeline {
    agent {
        label 'DevServer'
    }

    parameters {
        choice(
            name: 'select_environment',
            choices: ['dev', 'prod']
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
                script {
                    file = load "script.groovy"
                    file.hello()
                }
                sh 'mvn clean package -DskipTests=true'
            }
        }

        stage('test') {
            parallel {

                stage('testA') {
                    agent { label 'DevServer' }
                    steps {
                        echo "This is test A"
                        sh 'mvn test'
                    }
                }

                stage('testB') {
                    agent { label 'DevServer' }
                    steps {
                        echo "This is test B+"
                        sh 'mvn test'
                    }
                }
            }

            post {
                success {
                    stash name: 'frontend-build', includes: '**/*'
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
                dir('/home/ubuntu/deploy') {
                    unstash 'frontend-build'

                    sh '''
                        sudo rm -rf /var/www/html/*
                        sudo cp -r src/main/webapp/* /var/www/html/
                        sudo systemctl restart apache2
                    '''
                }
            }
        }

        stage('deploy_prod') {
            when {
                expression { params.select_environment == 'prod' }
                beforeAgent true
            }

            agent {
                label 'ProdServer'
            }

            steps {
                timeout(time: 5, unit: "DAYS") {
                    input message: 'Deployment approved?'

                    dir('/home/ubuntu/deploy') {
                        unstash 'frontend-build'

                        sh '''
                            sudo rm -rf /var/www/html/*
                            sudo cp -r src/main/webapp/* /var/www/html/
                            sudo systemctl restart apache2
                        '''
                    }
                }
            }
        }
    }
}
```
