pipeline {
    agent any
    tools {
        jdk 'java15amz'
        maven 'mvn3.5.3'
    }

    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    '''
            }
        }

        stage ('Build') {
            steps {
                def targetVersion = getDevVersion()
                print 'target build version...'
                print targetVersion
                sh "mvn -Dintegration-tests.skip=true clean package"
            }
        }
    }
}
