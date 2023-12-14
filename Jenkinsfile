pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    // Using a docker agent that runs one time after the stage has been completed
                    // containterizing your build and test stages helps with consistency across different environments and isolation to prevent conflicts and ensures that any changes made during the build and test stages do not affect the host system.
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile pythoncode/add2vals.py pythoncode/calc.py'
                stash(name: 'compiled-results', includes: 'pythoncode/*.py*')
                sh 'ls -l pythoncode'
            }
        }
        stage('Test') { 
            agent {
                docker {
                    image 'qnib/pytest' 
                }
            }
            steps {
                // --junit-mxl is used for generating a report which is saved in test-reports/results.xml. its a stanadrd practice doing this
                sh 'py.test --junit-xml test-reports/results.xml pythoncode/test_calc.py' 
                sh 'cat test-reports/results.xml'
            }
            post {
                always {
                    // this exposes the report/results on jenkins interface 
                    junit 'test-reports/results.xml' 
                }
            }
        }
    }
}
