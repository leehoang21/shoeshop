/* groovylint-disable LineLength */
pipeline {
    /* groovylint-disable-next-line NglParseError */
    // chỉ đinh agent
    //
    agent {
        label 'hoang-server'
    }
    environment {
        appUser = 'shoeshop'
        appName = 'shoe-ShopingCart'
        appVersion = '0.0.1-SNAPSHOT'
        appType = 'jar'
        processName = "${appName}-${appVersion}.${appType}"
        folderDeploy = "/datas/${appUser}"
        buildScript = 'mvn clean install -DskipTests=true'
        copyScript = "sudo cp target/${processName} ${folderDeploy}"
        permsScript = "sudo chown -R ${appUser}. ${folderDeploy}"
        /* groovylint-disable-next-line GStringExpressionWithinString */
        killScript = "sudo kill -9 \$(ps -ef| grep ${processName}| grep -v grep| awk '{print \$2}')"
        /* groovylint-disable-next-line GStringExpressionWithinString */
        runScript = 'sudo su ${appUser} -c "cd ${folderDeploy}; java -jar ${processName} --server.port=8081 > nohup.out 2>&1 &"'
    }
    stages {
        stage('build') {
            steps {
                sh(script: """ ${buildScript} """, label: 'build with maven')
            }
        }
        stage('deploy') {
            steps {
                script {
                    try {
                        timeout(time: 5, unit: 'MINUTES') {
                            /* groovylint-disable-next-line LineLength */
                            env.useChoice = input message: 'Can it be deployed?',
                                parameters: [choice(name: 'deploy', choices: 'no\nyes', description: 'Choose "yes" if you want to deploy!')]
                        }
                        if (env.useChoice == 'yes') {
                            sh(script: """ ${copyScript} """, label: 'copy the .jar file into deploy folder')
                            sh(script: """ ${permsScript} """, label: 'set permission folder')
                            // sh(script: """ ${killScript} """, label: 'terminate the running process')
                            sh(script: """ ${runScript} """, label: 'run the project')
                        }
                        else {
                            echo 'Do not confirm the deployment!'
                        }
                    /* groovylint-disable-next-line CatchException */
                    } catch (Exception err) {
                    }
                }
            }
        }
    }
}
