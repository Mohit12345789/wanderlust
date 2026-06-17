pipeline {
    agent any
    environment{
        SONAR_HOME = tool "sonar"
    } 
    stages {
        stage('Code Checkout') {
            steps {
                echo 'Cloning the code from GItHub'
                git url: "https://github.com/krishnaacharyaa/wanderlust.git" , branch: "devops"
            }
        }
        stage('Sonarqube quality Analysis') {
            steps {
                withSonarQubeEnv("sonar") {
                   sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectKey=wanderlust -Dsonar.projectName=wanderlust" 
                }
            }
        }
        stage('OWASP Dependency check') {
            steps {
                dependencyCheck additionalArguments: '--nvdApiKey ${NVD_API_KEY}' , odcInstallation: 'owasp-dc'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('Sonar quality gate scan') {
            steps {
                timeout(time: 2 , unit: "MINUTES"){
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage('Trivy file system Scam') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html . "
            }
        }
        stage('Deploy using Docker-Compose'){
        steps{
            sh "docker compose up -d"
            echo "All Thing are done your project is deployed on "
            }
        }
    }
}
