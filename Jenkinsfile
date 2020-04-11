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
                    sh "inspec exec \
                        --chef-license accept-silent \
                        -t ssh://devops:devops@192.168.33.37 \
                        --reporter cli junit:artifacts/testresults.xml \
                        linux-baseline"
                }                
            }
        }

    }
}
