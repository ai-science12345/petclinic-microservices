pipeline {
    agent any

    options {
        timestamps()
        timeout(time: 30, unit: 'MINUTES')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Maven Build') {
            steps {
                sh '''
                    export JAVA_HOME=/usr/lib/jvm/java-17-openjdk
                    export PATH="$JAVA_HOME/bin:$PATH"

                    java -version

                    ./mvnw --no-transfer-progress --batch-mode \
                        -DskipTests clean package
                '''
            }
        }

        stage('Verify JARs') {
            steps {
                sh '''
                    set -eu

                    COUNT=$(find spring-petclinic-*/target \
                        -maxdepth 1 -name '*.jar' -type f | wc -l)

                    echo "Generated JAR count: $COUNT"

                    test "$COUNT" -eq 8

                    find spring-petclinic-*/target \
                        -maxdepth 1 -name '*.jar' -type f
                '''
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: '**/target/*.jar',
                             fingerprint: true
            echo 'PETCLINIC CI BUILD SUCCESSFUL'
        }

        failure {
            echo 'PETCLINIC CI BUILD FAILED'
        }
    }
}
