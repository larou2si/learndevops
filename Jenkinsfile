CODE_CHANGES = getGitChanges() // !!!

/*
what variables are available from jenkins?
see at: <jenkins_url>/env-vars.html
*/
pipeline {
    agent none
    environment {
        NEW_VERSION = '1.3.0'
    }
    stages {
        stage("build") {
            when {
                expression {
                    BRANCH_NAME == 'dev' && CODE_CHANGES == true
                    // env.BRANCH_NAME == 'dev' && env.CODE_CHANGES == true
                }
            }
            steps {
                echo 'building the application... when the expression is validated!'
                echo "display an environment variable ${NEW_VERSION}"
            }
        }
        stage('Select micro services') {
            input {
                message "Select all micro services to deploy"
                ok "All selected!"
                parameters {
                    choice(name: 'MS1', choices: ['1.1.0', '1.2.0', '1.3.0'], description: 'input ms')
                    choice(name: 'MS2', choices: ['1.1.0', '1.2.0', '1.3.0'], description: 'input ms')
                    choice(name: 'MS3', choices: ['1.1.0', '1.2.0', '1.3.0'], description: 'input ms')
                    choice(name: 'MS4', choices: ['1.1.0', '1.2.0', '1.3.0'], description: 'input ms')
                }
            }
            steps {
                script {
                    echo "Hello, ${MS1}. Hello, ${MS2}. Hello, ${MS3}. Hello, ${MS4}"
                    MS1_TO_DEPLOY = MS1
                    MS2_TO_DEPLOY = MS2
                    env.MS3_TO_DEPLOY = MS3
                    env.MS4_TO_DEPLOY = MS4
                }
            }
        }
        stage('Select single service') {
            input {
                message "Select single micro services to deploy?"
                parameters {
                    choice(name: 'MS5', choices: ['1.1.0', '1.2.0', '1.3.0'], description: 'second param with single option')
                }
            }
            steps {
                script {
                    echo "Hello, ${MS5}."
                    env.MS5_TO_DEPLOY = MS5
                    echo "${MS1_TO_DEPLOY}"
                    echo "${MS4_TO_DEPLOY}"
                    echo "${MS5_TO_DEPLOY}"
                }
            }
        }
    }
    post {
        always {
            // this script part is always executed, for example send email no matter happen in the pipeline
        }
        success {
            //
        }
        failure {
            //
        }
    }
}

