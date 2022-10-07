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
        input message: "Lanjut ke deploy?"
    }
    stage('Deliver') {
        withEnv([
          "VOLUME=\$(pwd)/sources:/src",
          "IMAGE=cdrx/pyinstaller-linux:python2"
        ]) {
            dir(path: env.BUILD_ID) { 
              unstash(name: 'compiled-results') 
              sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
              archiveArtifacts "sources/dist/add2vals" 
              sleep(time:3,unit:"MINUTES")
            }
        }
    }
}

