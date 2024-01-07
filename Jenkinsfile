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



node {
    stage('Build') {
        docker.image('python:2-alpine') {
            sh 'python -m py_compile Jenkins/sources/add2vals.py Jenkins/sources/calc.py'
            }
       }
    stage('Test') {
        docker.image('qnib/pytest') {
            sh 'py.test --verbose --junit-xml test-reports/results.xml Jenkins/sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
       }  
}
}