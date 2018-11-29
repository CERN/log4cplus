pipeline {
    agent none
    stages {
        
        stage ('properties'){
            agent { label 'master' }
            steps {
                script {
                    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')), [$class: 'ScannerJobProperty', doNotScan: false], gitLabConnection('CERN GitLlab'), [$class: 'CopyArtifactPermissionProperty', projectNames: '*']])
                }
            }
        }
        
        stage ('Checkout'){
            parallel {
                stage('Windows') {
                    agent { label "windows" }
                    steps {
                        deleteDir()
	                    checkout([$class: 'GitSCM', branches: [[name: 'master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'SubmoduleOption', disableSubmodules: false, parentCredentials: false, recursiveSubmodules: true, reference: '', trackingSubmodules: false]], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/CERN/log4cplus']]])
                    }
                }
                stage('Unix') {
                    agent { label "unix" }
                    steps {
                        deleteDir()
	                    checkout([$class: 'GitSCM', branches: [[name: 'master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'SubmoduleOption', disableSubmodules: false, parentCredentials: false, recursiveSubmodules: true, reference: '', trackingSubmodules: false]], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/CERN/log4cplus']]])
                    }    
                }
            }
        }
        
        stage('Build') { 
            parallel {
                stage('Windows') {
                    agent { label "windows" }
                    steps {
                        script {
                            def msbuild = tool name: '14.0', type: 'msbuild'
                            bat "\"${msbuild}\" msvc14/log4cplus.vcxproj /p:Configuration=Release /p:Platform=\"x64\" "
                        }
                    }
                }
                stage('Unix') {
                    agent { label "unix" }
                    steps {
                       sh './configure --enable-so-version=no && autoreconf -f -i && make'
                    }    
                }
            }
        }
        
        stage ('Archive'){
            parallel {
                stage('Windows') {
                    agent { label "windows" }
                    steps {
                        archiveArtifacts artifacts:'msvc14/x64/bin.Release/*.*',fingerprint:true
                    }
                }
                stage('Unix') {
                    agent { label "unix" }
                    steps {
                        archiveArtifacts artifacts:'.libs/liblog4cplus-2.0.so',fingerprint:true
                        archiveArtifacts artifacts:'include/log4cplus/**/*.*',fingerprint:true 
                    }    
                }
            }
        }
    }
}
