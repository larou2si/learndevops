#!/usr/bin/env groovy

/*
    //import a shared library from a different repo
library identifier: 'jenkins-shared-library@master', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://gitlab.com/nanuchi/jenkins-shared-library.git',
    credentialsId: 'gitlab-credentials']
)

*/

CODE_CHANGES = getGitChanges() // !!!
def gv

/*
what variables are available from jenkins?
see at: <jenkins_url>/env-vars.html
*/
pipeline {
    agent none

    // declarative
    environment {
        NEW_VERSION = '1.3.0'
        /* acces to the credentials by declaring envirment variable ! or we
        SERVER_CRESENTIALS = credentials('server-crendentials')
        */
    }
    tools{ // preinstalled build tools in jenkins like maven, gradle...!
        maven 'Maven' // the name declared in jenkins config
        gradle
    }
    parameters{
        // string(name: 'VERSION', defaultValue: '', description: 'version to deploy')
        choice(name:'VERSION', choices:['1.1.0','1.2.0','1.3.0'], description: '....')
        booleanParam(name:'executeTests', defaultValue: true, description: '....')
    }
    // end declative

    stages {
        stage("init"){ // here in this stage, we load externel's file script
            steps{
                script{
                    gv = load "demoscript.groovy"
                }
            }
        }
        stage ('increment version'){
            steps {
                script {
                    eho 'incrementing app version...'
                    sh "mvn build-helper:parse-version versions:set \
                        -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                        versions:commit"
                // retrieve version values to match them with DockerImage
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    env.IMAGE_NAME = "$matcher[0][1]-$BUILD_NUMBER" 
                }
            }
        }
        stage ('build app') {
            steps {
                script{
                    echo "building our app....."
                    sh "mvn clean package"
                }
            }
        }
        stage("build image") {
            when {
                expression {
                    BRANCH_NAME == 'dev' && CODE_CHANGES == true
                    // env.BRANCH_NAME == 'dev' && env.CODE_CHANGES == true
                }
            }
            steps {
                echo 'building the application... when the expression is validated!'
                echo "display an environment variable ${NEW_VERSION}"
                withCredentials([
                    usernamePassword(credentialsID: 'docker-hub-repo', 
                        usernameVariable:USER, passwordVariable:PWD)
                ]){
                    sh "i can use the variable declare in usernameVariable ${USER} ${PWD}"
                    sh "docker build -t my-project-repo/demo-app:$env.IMAGE_NAME ."
                    sh "echo $PASS | docker login -u $USER --password-stdin <remote-repo>"
                    sh "docker push my-project-repo/demo-app:$env.IMAGE_NAME"
                }

            }
        }
        stage("test"){
            /*input{ // ALLOW USER PARAMETERS 
                message "select envirement to deploy to"
                ok "Done"
                parameters{
                    choice(name:'ONE', choices:['dev','staging','prod'], description: '..')
                    choice(name:'TWO', choices:['dev','staging','prod'], description: '..')

                }
            }*/
            when{
                expression{
                    params.executeTests
                }
            }
            steps{
                //echo "testing ${params.VERSION}"
                script{
                    // ALLOW USER PARAMETERS
                    env.ENV = input message: "select envirement to deploy to", ok: "Done", parameters: [choice(name:'ONE', choices:['dev','staging','prod'], description: '..')]

                    gv.testApp()
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

