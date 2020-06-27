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
            steps {
                sh '''
                    chmod +x bash_script.sh
                    gcc c_script.c -o c_script
                    chmod +x c_script
                '''
                script {
                    if (!fileExist('/logs/log.txt')) {
                        dir ('logs') {
                            writeFile file: 'log.txt', text: ''
                        }
                    }
                }
            }
        }
        stage ('All') {
            when {
                expression { params.Languages == 'All' }
            }
            steps {
                sh 'echo "Running All scripts"'
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
                sh 'echo "Running Bash script"'
                sh 'bash bash_script.sh'
            }
        }
        stage ('Python') {
            when {
                expression { params.Languages == 'Python' }
            }
            steps {
                sh 'echo "Running Python script"'
                sh 'python python_script.py'
            }
        }
        stage ('C') {
            when {
                expression { params.Languages == 'C' }
            }
            steps {
                sh 'echo "Running C script"'
                sh './c_script'
            }
        }
    }
    
    post {
        success {
            dir ('logs') {
                writeFile file: 'log.txt', text: "${readFile 'log.txt'} \n${INFO}, completed successfully with ${params.Languages} option on ${DATE}"
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
