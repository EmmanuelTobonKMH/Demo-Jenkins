def MOBBURL = ""

pipeline {
    agent any

    environment {
        MOBB_API_KEY = credentials('MOBB_API_KEY')
        CX_API_TOKEN = credentials('CX_API_TOKEN')
        GITHUBREPOURL = 'https://github.com/EmmanuelTobonKMH/Demo-Jenkins'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SAST') {
            steps {
                sh '''
                    apt-get update
                    apt-get install -y curl npm wget

                    curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
                    apt-get install -y nodejs

                    node -v
                    npm -v

                    npm install -g mobbdev

                    wget https://github.com/Checkmarx/ast-cli/releases/download/2.0.54/ast-cli_2.0.54_linux_x64.tar.gz -O checkmarx.tar.gz
                    tar -xf checkmarx.tar.gz

                    ./cx configure set --prop-name cx_apikey --prop-value $CX_API_TOKEN

                    ./cx scan create \
                      --project-name my-test-project \
                      -s ./ \
                      --report-format json \
                      --scan-types sast \
                      --branch main \
                      --threshold "sast-high=1"
                '''
            }
        }
    }

    post {

        success {
            echo 'Pipeline succeeded!'
        }

        failure {

            echo 'Pipeline failed! Running Mobb analysis...'

            script {
                MOBBURL = sh(
                    returnStdout: true,
                    script: '''
                        mobb analyze \
                          -f cx_result.json \
                          -r $GITHUBREPOURL \
                          --api-key $MOBB_API_KEY \
                          --ci
                    '''
                ).trim()
            }

            echo "Mobb Fix Link: ${MOBBURL}"
        }
    }
}
