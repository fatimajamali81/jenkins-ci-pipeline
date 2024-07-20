pipeline { 

    agent any 

    environment { 
        dotnet = 'C:\\Program Files\\dotnet\\dotnet.exe' 
    } 

    stages { 

        stage('Checkout Stage') { 
            steps { 
                withCredentials([string(credentialsId: 'GIT_ACCESS_TOKEN', variable: 'GIT_ACCESS_TOKEN')]) { 
                    git credentialsId: 'GIT_ACCESS_TOKEN', url: "https://$GIT_ACCESS_TOKEN@github.com/your-username/your-repo.git", branch: 'devops-branch' 
                } 
            } 
        } 

        stage('Restore packages') { 
            steps { 
                bat "dotnet restore C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\working" 
            } 
        } 

        stage('Build Stage') { 
            steps { 
                bat 'dotnet build C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\working\\SOL_ERP.sln --configuration Release' 
            } 
        } 

        stage("Deploy Stage") { 
            steps { 
                bat 'dotnet publish C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\working\\SOL_ERP.sln -c Release -o C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\working\\publish' 
            } 
        } 

        stage('Stop Site') { 
            steps { 
                bat '%windir%\\system32\\inetsrv\\appcmd stop sites "working"' 
                bat '%windir%\\system32\\inetsrv\\appcmd stop apppool /apppool.name:"working"' 
            } 
        } 

        stage('Start Maintenance Site') { 
            steps { 
                bat '%windir%\\system32\\inetsrv\\appcmd start sites "maintenance"' 
                bat '%windir%\\system32\\inetsrv\\appcmd start apppool /apppool.name:"maintenance"' 
            } 
        } 

        stage("Publish Stage") { 
            steps { 
                withCredentials([ 
                    usernamePassword( 
                        credentialsId: 'INSTANCE_CREDS',  
                        usernameVariable: 'INSTANCE_USER',  
                        passwordVariable: 'INSTANCE_PASS' 
                    ), 
                    string( 
                        credentialsId: 'INSTANCE_HOST',  
                        variable: 'INSTANCE_HOST' 
                    ) 
                ]) { 
                    bat 'IF EXIST Z: net use Z: /delete' 
                    bat 'net use Z: \\\\%INSTANCE_HOST%\\C$\\inetpub\\wwwroot\\working /user:%INSTANCE_USER% %INSTANCE_PASS%' 
                    bat 'xcopy "C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\working\\publish" Z:\\ /E /I /Y' 
                    bat 'net use Z: /delete' 
                } 
            } 
        } 

        stage('Stop Maintenance Site') { 
            steps { 
                bat '%windir%\\system32\\inetsrv\\appcmd stop sites "maintenance"' 
                bat '%windir%\\system32\\inetsrv\\appcmd stop apppool /apppool.name:"maintenance"' 
            } 
        } 

        stage('Start Site') { 
            steps { 
                bat '%windir%\\system32\\inetsrv\\appcmd start sites "working"' 
                bat '%windir%\\system32\\inetsrv\\appcmd start apppool /apppool.name:"working"' 
            } 
        } 

    } 

    post { 
        always { 
            emailext body: "Build result: ${currentBuild.result}\n\nConsole output: ${env.BUILD_URL}console", 
            recipientProviders: [[$class: 'CulpritsRecipientProvider'], [$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], 
            subject: "Build ${currentBuild.result}: ${env.JOB_NAME} #${env.BUILD_NUMBER}", 
            to: "your-email@example.com" 
        } 
    } 

} 
