pipeline{
    agent any
    environment{
        SONAR_HOME = tool "Sonar"
    }
    stages{
        stage("Clone Code from GitHub"){
            steps{
                echo "This is Code Phase"
                git url: "https://github.com/meeraparigi/DevSecOps-Wanderlust.git", branch: "devops"
            }
        }
        stage("SonarQube Quality Analysis"){
            steps{
                echo "This is SonarQube Quality Phase"
                withSonarQubeEnv("Sonar"){
                    sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=wanderlust -Dsonar.projectKey=wanderlust"
                }
            }
        }
        stage("OWASP Dependency Check"){
            steps{
                echo "This is OWASP Dependency Check Phase"
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'owasp-dc'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("Sonar Quality Gate Scan"){
            steps{
                echo "This is Sonar Quality Gate Scan Phase"
                timeout(time: 2, unit: "MINUTES"){
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage("Trivy File System Scan"){
            steps{
                echo "This is Trivy File System Scan Phase"
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        stage("Deploy using Docker Compose"){
            steps{
                echo "This is Deploy Phase using Docker Compose"
                sh "docker-compose up -d"
            }
        }
    }
}
