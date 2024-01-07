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



// node {
//     stage('Build') {
//         docker.image('python:2-alpine').inside("--entrypoint=''") {
//             sh 'python -m py_compile ./sources/add2vals.py ./sources/calc.py'
//             }
//        }
//     stage('Test') {
//         docker.image('qnib/pytest').inside("--entrypoint=''") {
//             sh 'py.test --verbose --junit-xml test-reports/results.xml s./ources/test_calc.py'
//             }
//             post {
//                 always {
//                     junit 'test-reports/results.xml'
//                 }
//        }  
// }
// }

node {
    def codeDir = '/tmp/code'  // Define a directory to store code in the Docker container

    stage('Build') {
        docker.image('python:2-alpine').inside("--entrypoint=''") {
            sh 'mkdir -p ' + codeDir  // Create a directory for code in the container
            
            // Create a tar archive of the sources directory
            sh 'tar -czf /tmp/code/sources.tar.gz -C /home/simple-python-pyinstaller-app sources'
            
            // Extract the tar archive inside the container
            sh 'tar -xzf /tmp/code/sources.tar.gz -C ' + codeDir
            sh 'python -m py_compile ' + codeDir + '/add2vals.py ' + codeDir + '/calc.py'
        }
    }

    stage('Test') {
        docker.image('qnib/pytest').inside("--entrypoint=''") {
            sh 'mkdir -p ' + codeDir  // Create a directory for code in the container
            
            // Extract the tar archive inside the container
            sh 'tar -xzf /tmp/code/sources.tar.gz -C ' + codeDir
            sh 'py.test --verbose --junit-xml test-reports/results.xml ' + codeDir + '/test_calc.py'
        }
        post {
            always {
                junit 'test-reports/results.xml'
            }
        }
    }
}
