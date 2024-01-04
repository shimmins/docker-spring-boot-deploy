pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: 'main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/shimmins/docker-spring-boot-deploy.git']]])
            }
        }

        stage('Argo Git Clone') {
            steps {
                git branch: 'main', credentialsId: 'git-jenkins', url: 'https://github.com/shimmins/docker-spring-boot-deploy.git'
                sh '''
                    rm -rf template/deployment.yaml
                    sed "s/VERSIONTAG/"${VERSION}"/g" "template/deployment-template.yaml" > template/deployment.yaml
                    ls -al
                    git add --all
                    git commit -m 'update image tag'
                '''
            }
        }
        
        stage('Credentials') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'git-jenkins', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    sh '''
                        git config --global user.name "${GIT_USERNAME}"
                        git config --global user.password "${GIT_PASSWORD}"
                        git push --set-upstream origin main
                        git config --global credential.helper store
                        git push origin main
                    '''
                }
            }
        }
        
    }
}
