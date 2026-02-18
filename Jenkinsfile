def MOBBURL = ""

pipeline {
    agent any

    environment {
        MOBB_API_KEY  = credentials('MOBB_API_KEY')
        CX_API_TOKEN  = credentials('CX_API_TOKEN')
        GITHUBREPOURL = 'https://github.com/EmmanuelTobonKMH/Demo-Jenkins'
        BRANCH_NAME   = 'main'
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
                echo "Downloading Checkmarx AST CLI..."
                curl -L -o checkmarx.tar.gz https://github.com/Checkmarx/ast-cli/releases/download/2.0.54/ast-cli_2.0.54_linux_x64.tar.gz
                tar -xf checkmarx.tar.gz

                echo "Configuring API Key..."
                ./cx configure set --prop-name cx_apikey --prop-value $CX_API_TOKEN

                echo "Running SAST Scan..."
                ./cx scan create \
                  --project-name my-test-project \
                  -s ./ \
                  --scan-types sast \
                  --branch $BRANCH_NAME \
                  --report-format json \
                  --output-path . \
                  --threshold "sast-high=1" || true
                '''
            }
        }
    }

    post {

        success {
            echo 'Pipeline completed without threshold violations.'
        }

        always {
            script {
    if (fileExists('cx_result.json')) {

        echo "Running Mobb Autofix..."

        try {
            MOBBURL = sh(
                returnStdout: true,
                script: '''
                npx mobbdev@latest analyze \
                  -f cx_result.json \
                  -r $GITHUBREPOURL \
                  --ref main \
                  --mobb-project-name Demo-Jenkins \
                  --api-key $MOBB_API_KEY \
                  --ci \
                  --polling || true
                '''
            ).trim()

            echo "======================================="
            echo "Mobb Fix Link:"
            echo "${MOBBURL}"
            echo "======================================="

        } catch (err) {
            echo "Mobb execution failed but pipeline will continue."
        }

    } else {
        echo "No cx_result.json found. Skipping Mobb."
    }
}

        }
    }
}
