pipeline{
    agent none
    stages{
        stage('Build'){
            agent {
                docker {
                    image 'python:3-alpine'
                }
            }
            steps{
                echo "========executing Build Stage========"
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
            post{
                always{
                    echo "========always========"
                }
                success{
                    echo "========Build executed successfully========"
                }
                failure{
                    echo "========Build execution failed========"
                }
            }
        }
        stage('Test'){
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps{
                echo "========executing Build Stage========"
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post{
                always{
                    junit 'test-reports/results.xml'
                }
                success{
                    echo "========Testing executed successfully========"
                }
                failure{
                    echo "========Testing execution failed========"
                }
            }
        }
        stage('Deliver'){
            agent any
            environment {
                VOLUME = '$(pwd)/sources:/src'
                IMAGE = 'cdrx/pyinstaller-linux:python2'
            }
            steps {
                dir(path: env.BUILD_ID) {
                    unstash(name: 'compiled-results')
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
                }
            }
            post {
                success {
                    archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
                }
            }
        }
    }
    post{
        always{
            echo "========always========"
        }
        success{
            echo "========pipeline executed successfully ========"
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
}