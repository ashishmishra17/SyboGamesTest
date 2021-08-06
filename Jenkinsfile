node {

    def repositoryURL = 'https://github.com/ashishmishra17/SyboGamesTest.git'

    def repositoryName = "[SyboGamesTest]"

    def projectName = "[NativeiOSApp]"

    def projectPath = "${WORKSPACE}/${repositoryName}/${projectName}"

    def archivePath = "${projectPath}/iOS/build/${projectName}.xcarchive"

    def buildBranchName = env.BRANCH_NAME

    stage('Git') {
          dir (repositoryName) {
            
             git( branch: buildBranchName, url: repositoryURL)
           }
       }
 
    stage('Unity Build') {

       def unityPath = "/Applications/Unity/Unity.app/Contents/MacOS/Unity"

       def executeMethod = "AppBuildBatch.BuildIOSDevelop"

    
       def buildTarget = "ios"

       def buildCommand = "${unityPath} -quit -batchmode"
           buildCommand += " -executeMethod ${executeMethod}"
           buildCommand += " -projectPath ${projectPath}"
           buildCommand += " -buildTarget ${buildTarget}"

       
           sh(script: buildCommand)
    }

    stage('xcode build')
       {
           def xcodeprojDir = "${projectPath}/iOS/"
           def xcodeproj = "${xcodeprojDir}.xcodeproj"

       
           def bundleID = "$(PRODUCT_BUNDLE_IDENTIFIER)"
           def displayName = "NativeiOS"
           def versionNumber = "1.0"
           def BuildNumber = "12"

           sh("/usr/libexec/Plistbuddy -c \"Set CFBundleIdentifier ${bundleID}\" ${xcodeprojDir}Info.plist")
           sh("/usr/libexec/Plistbuddy -c \"Set CFBundleDisplayName ${displayName}\" ${xcodeprojDir}Info.plist")
           sh("/usr/libexec/Plistbuddy -c \"Set CFBundleShortVersionString ${versionNumber}\" ${xcodeprojDir}Info.plist")
           sh("/usr/libexec/Plistbuddy -c \"Set CFBundleVersion ${BuildNumber}\" ${xcodeprojDir}Info.plist")

        
           sh(script: "xcodebuild clean -project ${xcodeproj}")

       
           def developmentTeamName = "SYBO"
           def provisioningProfilerSpecifier = "xxxxxxxxxxxxxxx"

           sh(script: "xcodebuild archive -project ${xcodeproj} -archivePath ${archivePath} -configuration Develop -scheme Unity-iPhone -sdk iphoneos10.3 CODE_SIGN_IDENTITY=\"iPhone Distribution\" PROVISIONING_PROFILE_SPECIFIER=${provisioningProfilerSpecifier} DEVELOPMENT_TEAM=${developmentTeamName}")
    }


    stage('ipa export')
        {
            def exportOptionFile = "${WORKSPACE}/build/buildparams/exportOptions.plist"

            def exportDir = "${WORKSPACE}/export/${BUILD_NUMBER}/"
            def exportFileName = "${projectName}_${BUILD_NUMBER}.ipa"

            dir ('export') {
                sh(script: "xcodebuild -exportArchive -archivePath ${archivePath} -exportPath ${exportDir} -exportOptionsPlist ${exportOptionFile}")

                if( fileExists("${BUILD_NUMBER}/Unity-iPhone.ipa") )
                 {
                    sh(script:"mv ${BUILD_NUMBER}/Unity-iPhone.ipa ${BUILD_NUMBER}/${exportFileName}")
                    archiveArtifacts(artifacts: "${BUILD_NUMBER}/${exportFileName}", onlyIfSuccessful: true)
                }
            }
        }
     }

