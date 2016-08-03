node {
    try {
        stage 'Build'
        def workspace = pwd() 
        deleteDir()
        checkout scm

        sh '''
            echo $PWD
            echo $BRANCH_NAME

            npm install gitbook-cli
            node_modules/gitbook-cli/bin/gitbook.js init
            node_modules/gitbook-cli/bin/gitbook.js build
            rm _book/Jenkinsfile
            mv _book pnda-guide-$BRANCH_NAME
            tar zcf pnda-guide-$BRANCH_NAME.tar.gz pnda-guide-$BRANCH_NAME
        '''

        stage 'Test'
        sh '''
        '''

        stage 'Deploy' 
        build job: 'deploy-component', parameters: [[$class: 'StringParameterValue', name: 'branch', value: env.BRANCH_NAME],[$class: 'StringParameterValue', name: 'component', value: "public-documentation"],[$class: 'StringParameterValue', name: 'release_path', value: "resources/releases"],[$class: 'StringParameterValue', name: 'release', value: "${workspace}/pnda-guide-${env.BRANCH_NAME}.tar.gz"]]

        emailext attachLog: true, body: "Build succeeded (see ${env.BUILD_URL})", subject: "[JENKINS] ${env.JOB_NAME} succeeded", to: "${env.EMAIL_RECIPIENTS}"

    }
    catch(error) {
        emailext attachLog: true, body: "Build failed (see ${env.BUILD_URL})", subject: "[JENKINS] ${env.JOB_NAME} failed", to: "${env.EMAIL_RECIPIENTS}"
        currentBuild.result = "FAILED"
        throw error
    }
}
