def pom, isMultiModuleBuild, pomModule
pipeline {
    agent none

    stages {
        stage('Checkout') {
            agent { label 'linux-security-1' }
            steps {
                checkout([$class: 'GitSCM',
                  branches: scm.branches,
                  extensions: scm.extensions + [[$class: 'WipeWorkspace']],
                  userRemoteConfigs: scm.userRemoteConfigs])
            }
        }

        stage('Run inspec tests') {
            agent { label 'linux-security-1' }
            steps {
                script {
                    datas = readYaml (file: 'inventaire.yml')

                    for (int i = 0; i < datas.size(); i++) {
                        item = datas[i]

                        checkout([  
                            $class: 'GitSCM', 
                            branches: [[name: 'refs/heads/master']], 
                            doGenerateSubmoduleConfigurations: false, 
                            extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: item.baseline.dir]], 
                            submoduleCfg: [], 
                            userRemoteConfigs: [[url: item.baseline.project]]
                        ])
                        
                        try {
                            sh "inspec exec \
                                --chef-license accept-silent \
                                ${item.baseline.dir} \
                                -t ssh://${item.ssh} \
                                --reporter cli junit:artifacts/${item.ip}.xml"
                        } catch (Exception e) {
                            echo("Le build a échoué à cause de inspec")
                        }finally{
                            sh "sed -i \"s/ classname='\\(.*\\)' target/ classname='\\${item.ip}' target/\" artifacts/${item.ip}.xml"
                        }
                    }
                }                
            }
            post {
                always {
                    junit 'artifacts/*.xml'
                }
            }
        }

    }
}
