node {
    stage('Build') {
        withDockerContainer('python:2-alpine') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', includes: 'sources/*.py*')
        }
    }
    stage('Test') {
        withDockerContainer('qnib/pytest') {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
    }
    stage('Deliver') {
        withDockerContaiener('cdrx/pyinstaller-linux:python2') {
            unstash(name: 'compiled-results') 
            sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'" 
            archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
        }
    }
}

