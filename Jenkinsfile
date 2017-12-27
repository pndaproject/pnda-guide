node {
    try {
        stage 'Build'
        
        deleteDir()
        
        checkout scm
        
        def workspace = pwd()
        def version = env.BRANCH_NAME

        if(env.BRANCH_NAME=="master") {
            version = sh(returnStdout: true, script: 'git describe --abbrev=0 --tags').trim()
            checkout([$class: 'GitSCM', branches: [[name: "tags/${version}"]], extensions: [[$class: 'CleanCheckout']]])
        }
        
        sh """
            npm install gitbook-cli
            node_modules/gitbook-cli/bin/gitbook.js init
            node_modules/gitbook-cli/bin/gitbook.js build
            rm -f _book/Jenkinsfile
            rm -f _book/instructions.md
            rm -f _book/pnda-guide.*
            rm -rf _book/archives
            mv _book pnda-guide-${version}
            tar zcf pnda-guide-${version}.tar.gz pnda-guide-${version}
        """

        stage 'Test'
        sh '''
        '''

        stage 'Deploy' 
        build job: 'deploy-component', parameters: [[$class: 'StringParameterValue', name: 'branch', value: env.BRANCH_NAME],[$class: 'StringParameterValue', name: 'component', value: "public-documentation"],[$class: 'StringParameterValue', name: 'release_path', value: "resources/releases"],[$class: 'StringParameterValue', name: 'release', value: "${workspace}/pnda-guide-${version}.tar.gz"]]

        if(env.BRANCH_NAME=="develop") {
            stage 'Publish' 
            build job: 'pnda-guide', parameters: [[$class: 'StringParameterValue', name: 'deployment', value: 'production'],[$class: 'StringParameterValue', name: 'release', value: env.BRANCH_NAME],[$class: 'StringParameterValue', name: 'release_path', value: "resources/releases"]]
        }
        
        stage 'Notifier'
        build job: 'notifier', parameters: [[$class: 'StringParameterValue', name: 'message', value: "${env.JOB_NAME} succeeded: see [Jenkins job ${env.BUILD_ID}](${env.BUILD_URL})"]] 
    }
    catch(error) {
        build job: 'notifier', parameters: [[$class: 'StringParameterValue', name: 'message', value: "${env.JOB_NAME} failed: see [Jenkins job ${env.BUILD_ID}](${env.BUILD_URL})"]]
        currentBuild.result = "FAILED"
        throw error
    }
}
