node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    stage('Test') {
        docker.image('qnib/pytest').inside {
            try {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            } finally {
                junit 'test-reports/results.xml'
            }
	    input message: 'Lanjutkan ke tahap Deploy?' 
        }
    }
    stage('Deploy') {
        docker.image('python:3.9').inside('-u root') {
	    sh 'pip install pyinstaller'
            sh 'pyinstaller --onefile sources/add2vals.py'
	    
            archiveArtifacts artifacts: 'dist/add2vals', allowEmptyArchive: false
	    
            sshagent(['cecdb662-6103-421a-95db-a4fc23e8879b']) {
            sh "scp -o StrictHostKeyChecking=no 'dist/add2vals' ubuntu@3.27.94.245:/home/ubuntu/"
            }
	    
            sleep time: 1, unit: 'MINUTES'
        }
    }
}
