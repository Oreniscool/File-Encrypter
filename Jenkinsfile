pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                // Use dir() to keep all commands scoped to the project folder
                dir("Password Protection") {
                    sh '''
                    echo "Building Java project..."
                    mkdir -p build
                    javac -d build src/*.java
                    '''
                }
            }
        }
        stage('Test') {
            steps {
                dir("Password Protection") {
                    sh '''
                    if [ ! -f junit-platform-console-standalone.jar ]; then
                        curl -L -o junit-platform-console-standalone.jar "https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar"
                    fi
                    
                    mkdir -p test-build
                    # Ensure the classpath points correctly to the build folder created earlier
                    javac -cp junit-platform-console-standalone.jar:build -d test-build test/*.java
                    
                    java -jar junit-platform-console-standalone.jar \
                        --class-path build:test-build \
                        --scan-class-path
                    '''
                }
            }
        }
        stage('Deploy') {
            steps {
                dir("Password Protection") {
                    sh 'jar cf FileEncrypter.jar -C build .'
                    // Archive the artifact so it doesn't just disappear when the job ends
                    archiveArtifacts artifacts: 'Password Protection/*.jar', fingerprint: true
                }
            }
        }
    }
    post {
        success { echo "Pipeline executed successfully!" }
        failure { echo "Pipeline failed!" }
    }
}
