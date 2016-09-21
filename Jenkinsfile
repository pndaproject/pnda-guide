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
            node_modules/gitbook-cli/bin/gitbook.js pdf ./ _book/downloads/pnda-guide.pdf
            node_modules/gitbook-cli/bin/gitbook.js epub ./ _book/downloads/pnda-guide.epub
            node_modules/gitbook-cli/bin/gitbook.js mobi ./ _book/downloads/pnda-guide.mobi
            mv _book pnda-guide-${version}
            tar zcf pnda-guide-${version}.tar.gz pnda-guide-${version}
        """

        stage 'Test'
        sh '''
        '''

        stage 'Deploy' 
        build job: 'deploy-component', parameters: [[$class: 'StringParameterValue', name: 'branch', value: env.BRANCH_NAME],[$class: 'StringParameterValue', name: 'component', value: "public-documentation"],[$class: 'StringParameterValue', name: 'release_path', value: "resources/releases"],[$class: 'StringParameterValue', name: 'release', value: "${workspace}/pnda-guide-${version}.tar.gz"]]

        emailext attachLog: true, body: "Build succeeded (see ${env.BUILD_URL})", subject: "[JENKINS] ${env.JOB_NAME} succeeded", to: "${env.EMAIL_RECIPIENTS}"

    }
    catch(error) {
        emailext attachLog: true, body: "Build failed (see ${env.BUILD_URL})", subject: "[JENKINS] ${env.JOB_NAME} failed", to: "${env.EMAIL_RECIPIENTS}"
        currentBuild.result = "FAILED"
        throw error
    }
}
