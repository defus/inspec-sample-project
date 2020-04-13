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
                    inventaires = readYaml (file: 'inventaire.yml')

                    for (int i = 0; i < inventaires.size(); i++) {
                        server = inventaires[i]

                        for (int j = 0; j < server.baselines.size(); i++) {
                            baseline = server.baselines[j]

                            checkout([  
                                $class: 'GitSCM', 
                                branches: [[name: 'refs/heads/master']], 
                                doGenerateSubmoduleConfigurations: false, 
                                extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: baseline.dir]], 
                                submoduleCfg: [], 
                                userRemoteConfigs: [[url: baseline.project]]
                            ])
                            
                            try {
                                if(server.remote){
                                    sh "inspec exec \
                                        --chef-license accept-silent \
                                        ${baseline.dir} \
                                        -t ssh://${server.ssh} \
                                        --reporter cli junit:artifacts/${server.ip}.xml"
                                }else{
                                    sh "inspec exec \
                                        --chef-license accept-silent \
                                        ${baseline.dir} \
                                        --reporter cli junit:artifacts/${server.ip}.xml"
                                }
                                
                            } catch (Exception e) {
                                echo("Le build a échoué à cause de inspec")
                            }finally{
                                sh "sed -i \"s/ classname='\\(.*\\)' target/ classname='\\1.${server.ip}' target/\" artifacts/${baseline.dir}-${server.ip}.xml"
                            }
                        }
                    }
                }
            }
            post {
                always {
                    //if (findFiles(glob: 'artifacts/*.xml')) {
                        junit 'artifacts/*.xml'
                    //}
                }
            }
        }

    }
}
