def MOBBURL

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
                checkout scmGit(
                    branches: [[name: '$ghprbActualCommit']],
                    extensions: [],
                    userRemoteConfigs: [[
                        credentialsId: '2760a171-4592-4fe0-84da-2c2f561c8c88',
                        refspec: '+refs/pull/*:refs/remotes/origin/pr/*',
                        url: "${GITHUBREPOURL}"
                    ]]
                )
            }
        }

        stage('SAST') {
    steps {
        sh '''
            apt-get update
            apt-get install -y curl

            curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
            apt-get install -y nodejs

            node -v
            npm -v

            wget https://github.com/Checkmarx/ast-cli/releases/download/2.0.54/ast-cli_2.0.54_linux_x64.tar.gz -O checkmarx.tar.gz
            tar -xf checkmarx.tar.gz

            ./cx configure set --prop-name cx_apikey --prop-value $CX_API_TOKEN

            ./cx scan create \
              --project-name my-test-project \
              -s ./ \
              --report-format json \
              --scan-types sast \
              --branch nobranch \
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
                      npx mobbdev@latest analyze \
                        -f cx_result.json \
                        -r $GITHUBREPOURL \
                        --ref $ghprbSourceBranch \
                        --api-key $MOBB_API_KEY \
                        --ci
                    '''
                ).trim()
            }

            echo "Mobb Fix Link: ${MOBBURL}"

            step([
                $class: 'GitHubCommitStatusSetter',
                commitShaSource: [
                    $class: 'ManuallyEnteredShaSource',
                    sha: "$ghprbActualCommit"
                ],
                contextSource: [
                    $class: 'ManuallyEnteredCommitContextSource',
                    context: 'Mobb Fix Link'
                ],
                reposSource: [
                    $class: 'ManuallyEnteredRepositorySource',
                    url: "$GITHUBREPOURL"
                ],
                statusBackrefSource: [
                    $class: 'ManuallyEnteredBackrefSource',
                    backref: "${MOBBURL}"
                ],
                statusResultSource: [
                    $class: 'ConditionalStatusResultSource',
                    results: [[
                        $class: 'AnyBuildResult',
                        message: 'Click on "Details" to access the Mobb Fix Link',
                        state: 'SUCCESS'
                    ]]
                ]
            ])
        }
    }
}
