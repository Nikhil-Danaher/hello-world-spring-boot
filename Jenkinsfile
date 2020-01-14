pipeline {
    agent any
    options {
        skipDefaultCheckout()
    }
    stages {
        stage('Checkout code') {
            steps {
                checkout scm
            }
        }
        stage('Docker login'){
            steps{
                withCredentials([usernamePassword(passwordVariable : 'DOCKER_PASSWORD', usernameVariable : 'DOCKER_USERNAME', \
                    credentialsId : "8ac0c641-f31f-410f-bba2-c86d7ac0d32a")]){
                    script
                    {
                        sh 'docker login -u "$DOCKER_USERNAME" -p "$DOCKER_PASSWORD" quay.io'
                    }
                }
            }
        }
        stage('Build step') {
            agent{
                docker {
                    image "quay.io/projectashton/base-java-ci:1.0"
                    reuseNode true
                }
            }
            steps {
                script{
                    dir("$env.WORKSPACE"){
                        STATUS = 0
                        STATUS = sh(returnStatus: true, script: 'gradle build')
                        if (STATUS == 0) {
                            echo ""
                        }
                        else {
                            echo ""
                            sh 'exit 1'
                        }
                    }
                }
            }
            post{
                always{
                    dir("$env.WORKSPACE"){
                    junit 'build/test-results/test/*.xml' 
                    }
                }
            }
        }
    //     stage('Coverage') {
    //         agent{
    //             docker {
    //                 image "quay.io/projectashton/base-python-ci:1.3"
    //                 reuseNode true
    //             }
    //         }
    //         steps {
    //             script{
    //                 dir("$env.WORKSPACE/${params.CI_NAME}"){
    //                     STATUS = sh(returnStatus: true, script: 'pytest testhandler.py')
    //                     sh(returnStatus: true, script: 'pytest testhandler.py --junitxml results.xml')
    //                     sh(returnStatus: true, script: 'coverage run -m unittest discover')
    //                     sh(returnStatus: true, script: 'coverage report testhandler.py > test')
    //                     sh(returnStatus: true, script: 'coverage xml testhandler.py')
    //                     sh(returnStatus: true, script: 'cp /home/jenkins/covergae_percentage.py .')
    //                     COVERAGE = sh(returnStdout: true, script: 'python3 covergae_percentage.py').trim()
    //                     echo "echo ########## coverage percentage is ${COVERAGE} ##########"
    //                     if (STATUS == 0) {
    //                         echo "############## Test cases passed ##############"
    //                         TEST_RESULT = "All test cases passed, check the link for info"
    //                     }
    //                     else {
    //                         echo "############## Test cases failed, check the report ##############"
    //                         TEST_RESULT = "Some test cases failed, check the link for info"
    //                         sh 'exit 1'
    //                     }
    //                 }
    //             }
    //         }
    //         post {
    //             always {
    //                 dir("$env.WORKSPACE/${params.CI_NAME}"){
    //                     junit 'results.xml'
    //                     step([$class: 'CoberturaPublisher',
    //                         autoUpdateHealth: false,
    //                         autoUpdateStability: false,
    //                         coberturaReportFile: 'coverage.xml',
    //                         failNoReports: false,
    //                         failUnhealthy: false,
    //                         failUnstable: false,
    //                         maxNumberOfBuilds: 10,
    //                         onlyStable: false,
    //                         sourceEncoding: 'ASCII',
    //                         zoomCoverageChart: false])
    //                 }
    //             }
    //             failure{
    //                 slackSend color: "danger", message: "${env.JOB_NAME} [${currentBuild.displayName}] ########### ${TEST_RESULT} ######### ${LINT_RESULT} ##########\
    //         afters ${currentBuild.durationString.replace(' and counting', '')} (<${currentBuild.absoluteUrl}|Open>)"
    //             }
    //         }
    //     }
    //     stage('Zipping the code') {
    //         steps {
    //             script{
    //                 dir("$env.WORKSPACE/${params.CI_NAME}"){
    //                     echo "Removing unnecessary files before zipping"
    //                     sh ('rm codestyle_score.py coverage.xml pylint.log result.py results.xml')
    //                 }
    //                  dir("$env.WORKSPACE"){
    //                     def PACKAGE_NAME = "${params.CI_NAME}"+"."+"${params.VERSION}"+"."+BUILD_NUMBER+".zip"
    //                     zip zipFile: "${PACKAGE_NAME}", archive: false, dir: "${params.CI_NAME}"
    //                     archiveArtifacts artifacts: "${PACKAGE_NAME}", fingerprint: true   
    //                     rtUpload (
    //                             serverId: 'Jfrog-artifactory',
    //                             spec: '''{
    //                             "files": [
    //                                     {
    //                                 "pattern": "*.zip",
    //                                 "target": "python-serverless/${params.CI_NAME}/"
    //                                     }
    //                                  ]
    //                             }''',
    //                 )
    //         }
    //     }
    // }
    // }
    }
    // post {
    //     success {
    //         slackSend color: "good", message: "${env.JOB_NAME} [${currentBuild.displayName}] ########### ${TEST_RESULT} ######### codestyle score is ${SCORE}, and coverage is ${COVERAGE}% ######## ${LINT_RESULT} #########\
    //         afters ${currentBuild.durationString.replace(' and counting', '')} (<${currentBuild.absoluteUrl}|Open>)"
    //     }
    //     always {
    //         cleanWs()   
    //     }
    // }
}
