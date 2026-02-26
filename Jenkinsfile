node {
    try {
        // First, we need to make sure the code is actually checked out
        stage('Checkout') {
            checkout scm
        }

        stage('Build') {
            // Using dir() ensures all commands inside happen in the subfolder
            dir("Password Protection") {
                sh '''
                echo "Building Java project..."
                mkdir -p build
                javac -d build src/*.java
                echo "Build successful"
                '''
            }
        }

        stage('Test') {
            dir("Password Protection") {
                sh '''
                echo "Running JUnit tests..."
                
                if [ ! -f junit-platform-console-standalone.jar ]; then
                    curl -L -o junit-platform-console-standalone.jar "https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar"
                fi

                mkdir -p test-build
                # We use the build folder we created in the previous stage
                javac -cp junit-platform-console-standalone.jar:build -d test-build test/*.java

                java -jar junit-platform-console-standalone.jar execute \
                    --class-path build:test-build \
                    --scan-class-path
                '''
            }
        }

        stage('Deploy') {
            dir("Password Protection") {
                sh '''
                echo "Packaging Application..."
                jar cf FileEncrypter.jar -C build .
                '''
                // In Scripted pipelines, archiveArtifacts is a standalone command
                archiveArtifacts artifacts: '*.jar', fingerprint: true
            }
        }

        echo "Pipeline executed successfully!"

    } catch (Exception e) {
        echo "Pipeline failed: ${e.message}"
        throw e
    }
}
