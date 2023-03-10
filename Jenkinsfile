
pipeline  {
    def autoCancelled = false
    agent { label 'kubeagent' }
    options {
        // This is required if you want to clean before build
        skipDefaultCheckout(true)
    }
    environment {
        GITHUB_PATH = "https://${env.GITHUB_APIKEY}@github.com/kdavid76/sm-parent.git"
    }
    tools {
        jdk 'oracle-jdk-17'
        maven 'mvn-3.8.7'
        git 'default'
    }
    stages {
        try
        {
            stage('Checkout') {
                steps {
                    cleanWs()
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: env.BRANCH_NAME]],
                        userRemoteConfigs: [[url: env.GITHUB_PATH]]
                    ])
                    bela = sh (
                        script: 'git log -1 --pretty=%B',
                        returnStdout: true
                    )
                    autoCancelled = true
                    echo "Bela: ${bela}"
                    error('Stopping early…')
                }
            }
        } catch (e) {
            if (autoCancelled) {
                currentBuild.result = 'SUCCESS'
                echo('Test if it's really doing it or not')
                // return here instead of throwing error to keep the build "green"
                return
            }
            // normal error handling
            throw e
        }
        stage('Build') {
            steps {
                sh ('mvn clean package')
            }
        }
    }
}