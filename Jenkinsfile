pipeline {
    agent any
    
    parameters {
        choice(name: 'Languages', choices: ['All', 'Bash', 'Python', 'C'], description: 'Choose a programming language:')
    }
    
    environment {
        DATE = sh(script: "date '+%d/%m/%Y %T'", returnStdout: true).trim()
        INFO = "Job: ${JOB_NAME} Build: ${BUILD_NUMBER}"
    }
    
    stages {
        stage ('Configure') {
            sh '''
                chmod +x bash_script.sh
                gcc c_script.c -o c_script
                chmod +x c_script
            '''
        }
        stage ('All') {
            when {
                expression { params.Languages == 'All' }
            }
            steps {
                parallel (
                    'Bash': {
                        sh "echo ${params.Languages} 1"
                    },
                    'Python': {
                        sh "echo ${params.Languages} 2"
                    },
                    'C': {
                        sh "echo ${params.Languages} 3"
                    }
                )
            }
        }
        stage ('Bash') {
            when {
                expression { params.Languages == 'Bash' }
            }
            steps {
                sh "echo ${params.Languages}"
            }
        }
        stage ('Python') {
            when {
                expression { params.Languages == 'Python' }
            }
            steps {
                sh "echo ${params.Languages}"
            }
        }
        stage ('C') {
            when {
                expression { params.Languages == 'C' }
            }
            steps {
                sh "echo ${params.Languages} ${DATE}"
            }
        }
    }
    
    post {
        success {
            dir ('logs') {
                writeFile file: 'log.txt', text: "${readFile 'log.txt'} \n${INFO}, completed successfully on ${DATE}"
            }
        }
        failure {
            dir ('logs') {
                writeFile file: 'log.txt', text: "${readFile 'log.txt'} \n${INFO}, failed on ${DATE}"
            }
        }
        aborted {
            dir ('logs') {
                writeFile file: 'log.txt', text: "${readFile 'log.txt'} \n${INFO}, was aborted on ${DATE}"
            }
        }
    }
}
