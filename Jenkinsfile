quest()



node("linux"){
    quest.util.notify {
        failChannels = ['pipeline-global-event']
        successChannels = ['pipeline-global-event']

        stage('Build HPI') {
            checkout scm
            sh 'mvn clean'
            sh 'mvn package'
        }

        stage('Upload to Artifactory'){
            def artifactory = Artifactory.server('dell-labs')

            def pom = readMavenPom file: 'pom.xml'
            def version = pom.version

            def target
            if(env.JOB_NAME =~ /PR-/){
                // matches the PR job name
                def shortJob = (env.JOB_NAME =~ /\/(PR-.*$)/)[0][1]
                target = "prs/cloudbees-bitbucket-branch-source-${version}-${shortJob}.hpi"
            } else {
                target = "${env.BRANCH_NAME}/cloudbees-bitbucket-branch-source-${version}.hpi"
            }

            def uploadSpec = """{"files": [
            {"pattern": "target/cloudbees-bitbucket-branch-source.hpi", "target": "casino-sbox/igable/bitbucket-branch-source-plugin/${target}", "flat": true}
            ]}"""

            artifactory.upload spec: uploadSpec
        }
    }
}
