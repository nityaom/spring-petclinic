// Jenkinsfile for Spring PetClinic CI Pipeline
// Developed by nityaom

pipeline {
    agent any

    triggers {
        // Cron trigger: "H/3 * * * 4" means every 3 minutes on Thursdays.
        cron('H/3 * * * 4')
    }

    stages {
        stage('Build & Package') {
            steps {
                // Use Maven wrapper to clean and package the project.
                sh './mvnw clean package'
            }
        }
        
        stage('Test & Jacoco Report') {
            steps {
                // Run tests and generate Jacoco code coverage report.
                sh './mvnw test jacoco:report'
            }
        }
    }
    
    post {
        always {
            // Archive the built artifact (JAR file).
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            
            // Publish JUnit test results.
            junit '**/target/surefire-reports/*.xml'
            
            // Publish the Jacoco HTML report for viewing in Jenkins.
            publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'target/site/jacoco',
                reportFiles: 'index.html',
                reportName: 'Jacoco Coverage Report'
            ])
        }
    }
}
