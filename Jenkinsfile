pipeline {
    agent any

    environment {
      SONAR_LOGIN = credentials('Sonar-Login-Token')
      GRADLE_PROPS = credentials('Nexus-Gradle-Props')
    }

    stages {
        stage('Compile') {
            steps {
              sh('cp $GRADLE_PROPS gradle.properties')
              gradlew('clean', 'classes')
            }
        }
        stage('Unit Tests') {
            steps {
                gradlew('test')
            }
            post {
                always {
                    junit '**/build/test-results/test/TEST-*.xml'
                }
            }
        }
        stage('Code Analysis') {
            steps {
                gradlew('sonarqube')
            }
        }
        stage('Assemble') {
            steps {
                gradlew('assemble')
            }
        }

        stage('Push to nexus'){
          steps{
            gradlew('publish')
          }
        }

        stage('Run AWX playbook'){
          steps{
            // milestone(1)
            // input('Deploy via AWX?')
            ansibleTower(
              towerServer: 'Dev Tower',
              jobTemplate: 'Deploy Spring Boot'
            )
          }
        }

    }
}

def gradlew(String... args) {
    sh "./gradlew ${args.join(' ')} -s"
}
