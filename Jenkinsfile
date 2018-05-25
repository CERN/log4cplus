pipeline{
  agent { label 'windows' }
  node {
    stage 'Checkout'
	checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'SubmoduleOption', disableSubmodules: false, parentCredentials: false, recursiveSubmodules: true, reference: '', trackingSubmodules: false]], submoduleCfg: [], userRemoteConfigs: [[]]])

    stage 'Build'
        dir msvc14
	bat "\"${tool 'MSBuild'}\" log4cplus.sln /p:Configuration=Release /p:Platform=\"win64\" "

    stage 'Archive'
	archive 'ProjectName/bin/Release/**'
  }

}
