pipeline {
    agent any
    environment {
        versionNumber = '1'
        registry = 'cunananan/spellbook'
        dockerHubCreds = 'dockerhub'
        dockerImage = ''
    }
    stages {
        stage("Analyzing code quality") {
            steps {
                echo '========> Analyzing code quality...'
                withSonarQubeEnv(credentialsId: 'sonar-token', installationName: 'sonar-qube') {
                    sh 'mvn -f app/pom.xml verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=030722-VA-SRE_team-epimetheus'
                }
            }
        }
        stage("Building Docker image") {
            steps {
                echo '========> Building Docker image...'
                script {
                    dockerImage = docker.build "$registry"
                }
            }
        }
        stage("Pushing image to DockerHub") {
            steps {
                echo '========> Pushing image to DockerHub...'
                script {
                    docker.withRegistry('', dockerHubCreds) {
                        dockerImage.push("$versionNumber.$currentBuild.number")
                        dockerImage.push("latest")
                    }
                }
            }
        }
        stage("Awaiting approval") {
            steps {
                echo '========> Awaiting approval...'
                script {
                    // Prompt, if yes build, if no abort
                    try {
                        timeout(time: 30, unit: 'MINUTES'){
                            approved = input message: 'Deploy to production?', ok: 'Continue',
                                parameters: [choice(name: 'approved', choices: 'Yes\nNo', description: 'Deploy this build to production')]
                            if(approved != 'Yes'){
                                error('Build not approved')
                            }
                        }
                    } catch (error){
                        error('Build not approved in time')
                    }
                }
            }
        }
        stage("Deploying to EKS") {
            steps {
                echo '========> Deploying to EKS'
                
            }
        }
    }
    // post {
    //     always {

    //     }
    //     success {

    //     }
    //     failure {

    //     }
    // }
}