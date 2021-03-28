pipeline {
    agent any
    tools {
        jdk 'java15amz'
        maven 'mvn3.5.3'
    }

    environment {
        RELEASE_BRANCH = "master"
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

        stage('Ask Version') {
            when {
                branch RELEASE_BRANCH
            }

            steps {
                script {
                    //Get current version
                    originalVersion = readMavenPom().getVersion()
                    if (!originalVersion.endsWith("-SNAPSHOT")) {
                        error("Trying to release a non-snapshot version")
                    }

                    // Calculate versions
                    releaseVersion = originalVersion.substring(0, originalVersion.size() - "-SNAPSHOT".size())
                    versionConstituents = releaseVersion.split("\\.")
                    versionConstituents[-1] = "${versionConstituents[-1].toInteger() + 1}".toString()
                    newSnapshotVersion = versionConstituents.join('.') + "-SNAPSHOT"
                }

                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        userInput = input(
                            id: 'userInput', message: 'VERSION', parameters: [
                            [$class: 'TextParameterDefinition', defaultValue: releaseVersion, description: 'Version to release', name: 'RELEASE_VERSION'],
                            [$class: 'TextParameterDefinition', defaultValue: newSnapshotVersion, description: 'Version to keep on repository to continue development', name: 'NEW_SNAPSHOT_VERSION']
                        ])
                    }
                }
                script {
                    releaseVersion = userInput['RELEASE_VERSION']
                    newSnapshotVersion = userInput['NEW_SNAPSHOT_VERSION']
                    //Change current build name
                    currentBuild.description = "Release $releaseVersion"
                    echo "Release Version: $releaseVersion"
                    echo "New snapshot version: $newSnapshotVersion"
                }
            }
        }

        stage('Test') {
            steps {
                sh "mvn --batch-mode -V -U -e clean test -Dsurefire.useFile=false"
            }
        }
    }
}
