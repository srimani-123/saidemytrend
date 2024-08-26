pipeline {
    // Specify the agent to run the pipeline
    agent any
    
    // Set environment variables for the pipeline
    environment {
        PATH = "/opt/maven/bin:$PATH"
    }
    
    // Define the stages of the pipeline
    stages {
        // Stage for checking out the repository
        stage('Checkout') {
            steps {
                // Clone the GitHub repository
                git url: 'https://github.com/SaiDevOpsFaculty/saidemytrend.git', branch: 'main'
            }
        }
        
        // Stage for building the project
        stage('Build') {
            steps {
                // Run Maven clean and deploy commands
                sh 'mvn clean deploy'
            }
        }
        
        // Stage for SonarQube analysis
        stage('SonarQube Analysis') {
            environment {
                // Set the SonarQube scanner tool
                scannerHome = tool 'srimani_sonar_scanner'
            }
            steps {
                // Execute SonarQube analysis within the SonarQube environment
                withSonarQubeEnv('srimani_sonarqubeserver') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
    }
}

