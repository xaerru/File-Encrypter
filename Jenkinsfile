pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                dir('Password Protection') {
                    sh '''
                        set -e
                        echo "Building Java project..."
                        echo "Listing workspace contents:"
                        ls

                        mkdir -p build
                        javac -d build src/*.java

                        echo "Build successful"
                    '''
                }
            }
        }

        stage('Test') {
            steps {
                dir('Password Protection') {
                    sh '''
                        set -e
                        echo "Running JUnit tests..."

                        rm junit-platform-console-standalone.jar
                        # Download JUnit jar if not already present
                        if [ ! -f junit-platform-console-standalone.jar ]; then
                            echo "Downloading JUnit..."
                            curl -f -L -o junit-platform-console-standalone.jar \
                            https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.0/junit-platform-console-standalone-1.10.0.jar
                        fi

                        mkdir -p test-build

                        # Compile test files
                        javac -cp junit-platform-console-standalone.jar:build \
                              -d test-build \
                              test/*.java

                        # Run JUnit tests
                        java -jar junit-platform-console-standalone.jar \
                             --class-path build:test-build \
                             --scan-class-path

                        echo "JUnit tests executed successfully"
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                dir('Password Protection') {
                    sh '''
                        set -e
                        echo "Packaging File-Encrypter Application..."

                        jar cf FileEncrypter.jar -C build .

                        echo "Deployment successful - Artifact ready"
                    '''
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
