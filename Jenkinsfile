def MOBBURL

pipeline {
    agent any

    environment {
        MOBB_API_KEY = credentials('MOBB_API_KEY')
        CX_API_TOKEN = credentials('CX_API_TOKEN')
        GITHUBREPOURL = 'https://github.com/EmmanuelTobonKMH/Demo-Jenkins'
        BRANCH_NAME = 'main'
    }

    tools {
        nodejs 'NodeJS'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SAST - Checkmarx') {
            steps {
                sh '''
                curl -L -o checkmarx.tar.gz https://github.com/Checkmarx/ast-cli/releases/download/2.0.54/ast-cli_2.0.54_linux_x64.tar.gz
                tar -xf checkmarx.tar.gz

                ./cx configure set --prop-name cx_apikey --prop-value $CX_API_TOKEN

                ./cx scan create \
                  --project-name my-test-project \
                  -s ./ \
                  --scan-types sast \
                  --branch $BRANCH_NAME \
                  --report-format json \
                  --output-path cx_result.json \
                '''
            }
        }
    }

    post {

        success {
            echo 'Pipeline succeeded!'
        }

        failure {
            echo 'SAST failed - Running Mobb Autofix...'

            script {
                MOBBURL = sh(
                    returnStdout: true,
                    script: '''
                    npx mobbdev@latest analyze \
                      -f cx_result.json \
                      -r $GITHUBREPOURL \
                      --ref main \
                      --mobb-project-name Demo-Jenkins \
                      --api-key $MOBB_API_KEY \
                      --ci
                    '''
                ).trim()
            }

            echo "Mobb Fix Link: ${MOBBURL}"
        }
    }
}
