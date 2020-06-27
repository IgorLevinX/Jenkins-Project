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
                        sh 'bash bash_script.sh'
                    },
                    'Python': {
                        sh 'python python_script.py'
                    },
                    'C': {
                        sh './c_script'
                    }
                )
            }
        }
        stage ('Bash') {
            when {
                expression { params.Languages == 'Bash' }
            }
            steps {
                sh 'bash bash_script.sh'
            }
        }
        stage ('Python') {
            when {
                expression { params.Languages == 'Python' }
            }
            steps {
                sh 'python python_script.py'
            }
        }
        stage ('C') {
            when {
                expression { params.Languages == 'C' }
            }
            steps {
                sh './c_script'
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
