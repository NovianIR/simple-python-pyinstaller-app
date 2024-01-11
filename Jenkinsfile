// pipeline {
//     agent none
//      environment {
//         GITHUB_TOKEN     = credentials('jenkins-github-token')
//         GITHUB_REPOSITORY = 'NovianIR/simple-python-pyinstaller-app'
//     }
// stages {
//         stage('Build') {
//             agent {
//                 docker {
//                     image 'python:2-alpine'
//                 }
//             }
//             steps {
//                 sh 'python -m py_compile sources/add2vals.py sources/calc.py'
//             }
//         }
//         stage('Test') {
//             agent {
//                 docker {
//                     image 'qnib/pytest'
//                 }
//             }
//             steps {
//                 sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
//             }
//             post {
//                 always {
//                     junit 'test-reports/results.xml'
//                 }
//             }
//         }
// stage('Deploy') {
//             agent {
//                 docker {
//                     image 'cdrx/pyinstaller-linux:python2'
//                 }
//             }
//             steps {
//                 input(message: 'Lanjutkan ke tahap Deploy?', submitter: 'user1,user2', submitterParameter: 'APPROVE')
//                 sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
//                 sh 'chmod +x ./jenkins/scripts/github-pages.sh && ./jenkins/scripts/github-pages.sh'
//                 sh 'sleep 60'
//             }
//             post {
//              post {
//                 success {
//                     archiveArtifacts 'dist/add2vals'
//                 }
//             }
//         }
//     }
// }
// }

node {
    stage('Build') {
        docker.image('python:2-alpine').inside("--entrypoint=''") {
            checkout scm
            sh 'python -m py_compile ./sources/add2vals.py ./sources/calc.py'
            }
       }

    stage('Test') {
        docker.image('qnib/pytest').inside("--entrypoint=''") {
            checkout scm
            sh 'py.test --verbose --junit-xml test-reports/results.xml ./sources/test_calc.py'
            }
    }
    stage('Deploy') {
        input(message: 'Lanjutkan ke tahap Deploy?', submitter: 'user1,user2', submitterParameter: 'APPROVE')
        docker.image('cdrx/pyinstaller-linux:python2').inside("--entrypoint=''") {
             'pyinstaller --onefile sources/add2vals.py'
            //   sh 'sleep 60'
             }
             // Add the deployment to GitHub Pages
        script {
            def ghPagesDir = "${WORKSPACE}/gh-pages"

            // Clone the repository again to a different directory
            checkout([$class: 'GitSCM',
                      branches: [[name: '*/gh-pages']],
                      doGenerateSubmoduleConfigurations: false,
                      extensions: [[$class: 'CleanBeforeCheckout']],
                      submoduleCfg: [],
                      userRemoteConfigs: [[url: 'https://github.com/NovianIR/simple-python-pyinstaller-app.git']]])

            // Create or clean the gh-pages branch
            sh "git checkout -B gh-pages"

            // Copy the built files to the gh-pages branch
            sh "cp -r ${WORKSPACE}/dist/* ${ghPagesDir}"

            // Commit and push changes to gh-pages branch
            sh "git add ."
            sh "git commit -m 'Deploy to GitHub Pages'"
            sh "git push origin gh-pages"

            // Clean up the gh-pages directory
            sh "rm -rf ${ghPagesDir}"
            
}
}
}

