pipeline {
    agent {
        label 'DevServer'
    }
    parameters {
  string defaultValue: 'LEO', name: 'LASTNAME'
}

environment
{
    NAME = "Messi"
}
    tools {
        maven 'mymaven'
    }

    stages {
        stage('build') {
            steps {
                sh 'mvn clean package'
                echo "hello $NAME ${params.LASTNAME}"
            }

            post {
                success {
                    archiveArtifacts artifacts: '**/target/*.jar'
                }
            }
        }
    }
}