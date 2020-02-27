node {
    def mvnHome
    stage('Checkout') {
        git branch: git_branch, credentialsId: "gitlab-account-portal", url: 'http://10.119.169.7/CDD/poc-customer-portal.git'
    }
    stage('Code scan') {
     sh '/apps/sonar-scanner-3.3.0.1492-linux/bin/sonar-scanner'
    }
    stage('Module copy') {
     sh 'unzip -u /apps/npm_repo/node_modules.zip -d ./'
    }
    stage('Build') {
     sh 'ng build --configuration=$ENV'
    }
    stage('Tar') {
     sh 'tar -cvf ./dist.tar ./dist'
    }
    stage('Deployment') {
      if (ENV == 'production') {
        sshPublisher(publishers: [sshPublisherDesc(configName: 'cdd_nj_dev_server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'poc-customer-portal', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'dist.tar')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
      } else if (ENV == 'test') {
        sshPublisher(publishers: [sshPublisherDesc(configName: 'cdd_nj_dev_server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'test/poc-customer-portal', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'dist.tar')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
      }
    }
    stage('Start service') {
      def remote = [:]
      remote.name = 'appdeployer'
      remote.host = '10.119.169.73'
      remote.user = 'appdeployer'
      remote.password = dev_sev_password
      remote.allowAnyHosts = true
      if (ENV == 'production') {
        sshCommand remote: remote, command: "tar -xvf /apps/poc-customer-portal/dist.tar -C /apps/poc-customer-portal/ && cp -rf /apps/poc-customer-portal/dist/poc-customer-portal/* /apps/poc-customer-portal/web/ && rm /apps/poc-customer-portal/dist.tar && rm -rf /apps/poc-customer-portal/dist"
      } else if (ENV == 'test') {
        sshCommand remote: remote, command: "tar -xvf /apps/test/poc-customer-portal/dist.tar -C /apps/test/poc-customer-portal/ && cp -rf /apps/test/poc-customer-portal/dist/poc-customer-portal/* /apps/test/poc-customer-portal/web/ && rm /apps/test/poc-customer-portal/dist.tar && rm -rf /apps/test/poc-customer-portal/dist"
      }
    }
}
