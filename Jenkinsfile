// Jenkins pipeline for prinitng Hello World in different langueges (Bash, Python, C or all of them).
// After the first build completes a refersh or reload of the jenkins page is needed.
pipeline {
    agent any
    
    // Choice parameters for the job to build in different languages
    parameters {
        choice(name: 'Languages', choices: ['All', 'Bash', 'Python', 'C'], description: 'Choose a programming language:')
    }
    
    // Enviroment varaibles to use in all of the job scopes
    environment {
        DATE = sh(script: "date '+%d/%m/%Y %T'", returnStdout: true).trim()
        INFO = "Job: ${JOB_NAME} Build: ${BUILD_NUMBER}"
    }
    
    // The main stages of the job
    stages {
        // The first configuration stage to build the prerequisites for the scripts and logs. 
        stage ('Configure') {
            steps {
                sh '''
                    chmod +x bash_script.sh
                    gcc c_script.c -o c_script
                    chmod +x c_script
                '''
                script {
                    if (!fileExists('/logs/log.txt')) {
                        dir ('logs') {
                            writeFile file: 'log.txt', text: ''
                        }
                    }
                }
            }
        }
        // Choosing the 'All' option to run the scripts of Bash, Pythin and C in parallel
        stage ('All') {
            when {
                expression { params.Languages == 'All' }
            }
            steps {
                parallel (
                    'Message': {
                        sh 'echo "Running All scripts"'
                    },
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
        // Choosing the 'Bash' option to run Bash script
        stage ('Bash') {
            when {
                expression { params.Languages == 'Bash' }
            }
            steps {
                sh 'echo "Running Bash script"'
                sh 'bash bash_script.sh'
            }
        }
        // Choosing the 'Python' option to run Python script
        stage ('Python') {
            when {
                expression { params.Languages == 'Python' }
            }
            steps {
                sh 'echo "Running Python script"'
                sh 'python python_script.py'
            }
        }
        // Choosing the 'C' option to run C script
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
    
    // Logging the state of the build job to a log file
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
