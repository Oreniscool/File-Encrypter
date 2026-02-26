pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                dir("Password Protection") {
                    sh '''
                    echo "Building Java project..."
                    mkdir -p build
                    javac -d build src/*.java
                    echo "Build successful"
                    '''
                }
            }
        }

        stage('Test') {
            steps {
                dir("Password Protection") {
                    sh '''
                    echo "Running JUnit tests for File-Encrypter..."
                    
                    # Download JUnit jar if not already present
                    if [ ! -f junit-platform-console-standalone.jar ]; then
                        echo "Downloading JUnit..."
                        curl -L -o junit-platform-console-standalone.jar \
                        "https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar"
                    fi

                    # Compile test files
                    mkdir -p test-build
                    javac -cp junit-platform-console-standalone.jar:build -d test-build test/*.java

                    # Run JUnit tests using the 'execute' command to avoid deprecation warnings
                    java -jar junit-platform-console-standalone.jar execute \
                        --class-path build:test-build \
                        --scan-class-path
                    
                    echo "JUnit tests executed successfully"
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                dir("Password Protection") {
                    sh '''
                    echo "Deploying (Packaging) File-Encrypter Application..."
                    # Create executable artifact (JAR)
                    jar cf FileEncrypter.jar -C build .
                    echo "Deployment successful - Artifact ready"
                    '''
                    // Fixed path: Since we are inside the 'Password Protection' dir, 
                    // we look for the jar in the current folder (.)
                    archiveArtifacts artifacts: '*.jar', fingerprint: true
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
