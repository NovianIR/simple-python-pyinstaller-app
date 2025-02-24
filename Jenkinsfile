pipeline {
    agent none
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:3.12.1-alpine3.19'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Manual Approval') {
            steps {
                input(message: 'Lanjutkan ke tahap Deploy?', submitter: 'user1,user2', submitterParameter: 'APPROVE')
            }
        }
        
        stage('Deploy') { 
            agent any
            environment { 
                VOLUME = '$(pwd)/sources:/src'
                IMAGE = 'cdrx/pyinstaller-linux:python2'
            }
            steps {
                dir(path: env.BUILD_ID) { 
                    unstash(name: 'compiled-results') 
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'" 
                    sh 'sleep 60'
                }
            }
            post {
                success {
                    archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
                    script{
                        sshagent (credentials: ['jenkins-cloud']){
                            sh "scp -o StrictHostKeyChecking=no -r ${env.BUILD_ID}/sources/dist/add2vals ubuntu@18.136.211.111:/home/ubuntu/PythonApp"
                            sh "ssh -o StrictHostKeyChecking=no ubuntu@18.136.211.111 chmod +x /home/ubuntu/PythonApp/add2vals"
                            sh "ssh -o StrictHostKeyChecking=no ubuntu@18.136.211.111 /home/ubuntu/PythonApp/add2vals 25 43"
                        }
                        sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
                    }
                }
            }
        }
    }
}



// node {
//     stage('Build') {
//         docker.image('python:2-alpine').inside("--entrypoint=''") {
//             checkout scm
//             sh 'python -m py_compile ./sources/add2vals.py ./sources/calc.py'
//             }
//        }

//     stage('Test') {
//         docker.image('qnib/pytest').inside("--entrypoint=''") {
//             checkout scm
//             sh 'py.test --verbose --junit-xml test-reports/results.xml ./sources/test_calc.py'
//             }
//     }
//     stage('Deploy') {
//         input(message: 'Lanjutkan ke tahap Deploy?', submitter: 'user1,user2', submitterParameter: 'APPROVE')
//         docker.image('cdrx/pyinstaller-linux:python2').inside("--entrypoint=''") {
//              'pyinstaller --onefile sources/add2vals.py'
//               sh 'sleep 60'
//              }
     
            
// }
// }

