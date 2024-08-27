def registry = 'https://srimani123.jfrog.io/'

pipeline {
    // Specify the agent to run the pipeline
    agent any

    // Set environment variables for the pipeline
    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    // Define the stages of the pipeline
    stages {
        // Stage for building the project
        stage('build') {
            steps {
                // Log message to indicate build start
                echo "-----Build started-----"
                // Run Maven clean and deploy commands, skipping tests
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                // Log message to indicate build completion
                echo "-----Build completed-----"
            }
        }

        // Stage for running unit tests
        stage('test') {
            steps {
                // Log message to indicate unit test start
                echo "-----Unit test started-----"
                // Run Maven Surefire report
                sh 'mvn surefire-report:report'
                // Log message to indicate unit test completion
                echo '-----Unit test completed-----'
            }
        }

        // Stage for SonarQube analysis
        stage('SonarQube analysis') {
            environment {
                // Set the SonarQube scanner tool
                scannerHome = tool 'srimani-sonar-scanner'
            }
            steps {
                // Execute SonarQube analysis within the SonarQube environment
                withSonarQubeEnv('srimani-sonarqube-server') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        // New Stage for Jar Publishing
        stage("Jar Publish") {
            steps {
                script {
                    echo "----------- Jar Publish Started -----------"
                    def server = Artifactory.newServer url: "${registry}/artifactory", credentialsId: "artifact-cred"
                    def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "jarstaging/*",
                                "target": "srimani-libs-release-local/{1}",
                                "flat": false,
                                "props": "${properties}",
                                "exclusions": [ "*.sha1", "*.md5" ]
                            }
                        ]
                    }"""
                    def buildInfo = server.upload(uploadSpec)
                    buildInfo.env.collect()
                    server.publishBuildInfo(buildInfo)
                    echo "----------- Jar Publish Ended -----------"
                }
            }
        }
    }

    // Post-build actions
    post {
        always {
            echo 'Cleaning up...'
            // Add any cleanup steps if necessary
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
            // Optionally send notifications or trigger other actions
        }
    }
}

