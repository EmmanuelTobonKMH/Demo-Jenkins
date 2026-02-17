def MOBBURL

pipeline {
    agent any
    // Setting up environment variables
    environment {
        MOBB_API_KEY = credentials('MOBB_API_KEY')
        CX_API_TOKEN = credentials('CX_API_TOKEN')
        GITHUBREPOURL = 'https://github.com/EmmanuelTobonKMH/Demo-Jenkins.git' //change this to your GitHub Repository URL
    }
    tools {
        nodejs 'NodeJS'
    }
    stages {
        // Checkout the source code from the branch being committed
        stage('Checkout') {
            steps {
                checkout scmGit(
                    branches: [[name: '$ghprbActualCommit']], 
                    extensions: [], 
                    userRemoteConfigs: [[
                        credentialsId: '2760a171-4592-4fe0-84da-2c2f561c8c88', 
                        refspec: '+refs/pull/*:refs/remotes/origin/pr/*', 
                        url: "${GITHUBREPOURL}"]]
                        )

            }
        }
        // Run SAST scan
        stage('SAST') {
            steps {
                sh 'wget https://github.com/Checkmarx/ast-cli/releases/download/2.0.54/ast-cli_2.0.54_linux_x64.tar.gz -O checkmarx.tar.gz'
                sh 'tar -xf checkmarx.tar.gz'    
                sh './cx configure set --prop-name cx_apikey --prop-value $CX_API_TOKEN'
                sh './cx scan create --project-name my-test-project -s ./ --report-format json --scan-types sast --branch nobranch  --threshold "sast-high=1"'
            }
        }
    }
    post {
        // If SAST scan complete with no issues found, pipeline is successful
        success {
            echo 'Pipeline succeeded!'
        }
        // If SAST scan complete WITH issues found, pipeline enters fail state, triggering Mobb autofix analysis
        failure {
            echo 'Pipeline failed!'

                script {
                    MOBBURL = sh(returnStdout: true,
                                script:'npx mobbdev@latest analyze -f cx_result.json -r $GITHUBREPOURL --ref $ghprbSourceBranch --api-key $MOBB_API_KEY  --ci')
                                .trim()
                }     
            echo 'Mobb Fix Link: $MOBBURL'
            // Provide a "Mobb Fix Link" in the GitHub pull request page as a commit status
            step([$class: 'GitHubCommitStatusSetter', 
                    commitShaSource: [$class: 'ManuallyEnteredShaSource', sha: '$ghprbActualCommit'], 
                    contextSource: [$class: 'ManuallyEnteredCommitContextSource', context: 'Mobb Fix Link'], 
                    reposSource: [$class: 'ManuallyEnteredRepositorySource', url: '$GITHUBREPOURL'], 
                    statusBackrefSource: [$class: 'ManuallyEnteredBackrefSource', backref: "${MOBBURL}"], 
                    statusResultSource: [$class: 'ConditionalStatusResultSource', 
                        results: [[$class: 'AnyBuildResult', message: 'Click on "Details" to access the Mobb Fix Link', state: 'SUCCESS']]]
            ])
        }
    }
}