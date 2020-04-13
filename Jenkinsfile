def pom, isMultiModuleBuild, pomModule
pipeline {
    agent none

    stages {
        stage('Checkout') {
            agent { label 'linux-security-1' }
            steps {
                checkout([$class: 'GitSCM'])
            }
        }

        stage('Run inspec tests') {
            agent { label 'linux-security-1' }
            steps {
                script {
                    //S'assurer que le repertoire des contrôles est bien en place sur le serveur Jenkins security
                    checkout([  
                        $class: 'GitSCM', 
                        branches: [[name: 'refs/heads/master']], 
                        doGenerateSubmoduleConfigurations: false, 
                        extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'linux-baseline']], 
                        submoduleCfg: [], 
                        userRemoteConfigs: [[url: 'https://github.com/dev-sec/linux-baseline.git']]
                    ])
                    
                    //Recupérer le projet security : projet Ansible avec l'inventaire des infrastructures et pour chaque infra ses serveurs

                    //Lancer le test avec le descripteur de tests mis sur le projet
                    try {
                        sh "inspec exec \
                            --chef-license accept-silent \
                            linux-baseline \
                            -t ssh://devops:devops@192.168.33.37 \
                            --reporter cli junit:artifacts/192.168.33.37.xml"
                    } catch (Exception e) {
                        echo("Le build a échoué à cause de inspec")
                    }finally{
                         sh "sed -i \"s/<testsuite name='\(.*\)' tests/<testsuite name='\1 192.168.33.37' tests/\" artifacts/192.168.33.37.xml"
                    }

                    try {
                        sh "inspec exec \
                            --chef-license accept-silent \
                            linux-baseline \
                            --reporter cli junit:artifacts/192.168.33.200.xml"
                    } catch (Exception e) {
                        echo("Le build a échoué à cause de inspec")
                    }finally{
                        sh "sed -i \"s/<testsuite name='\(.*\)' tests/<testsuite name='\1 192.168.33.200' tests/\" artifacts/192.168.33.200.xml"
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
