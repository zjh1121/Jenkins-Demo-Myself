pipeline {
    agent any

    stages {
        stage('查看工作目录') {
            steps {
                sh 'pwd'
                sh 'ls -la'
            }
        }

        stage('CI - 检查静态页面') {
            steps {
                sh '''
                    test -f index.html
                    echo "index.html 文件存在"

                    grep -q "Jenkins" index.html
                    echo "页面内容检查通过"
                '''
            }
        }

        stage('CD - 部署到服务器') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'server-root-ssh-pem',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    sh '''
                        ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no "$SSH_USER"@8.136.188.3 "mkdir -p /var/www/html/jenkins-demo"

                        scp -i "$SSH_KEY" -o StrictHostKeyChecking=no index.html "$SSH_USER"@8.136.188.3:/var/www/html/jenkins-demo/index.html
                    '''
                }
            }
        }

        stage('CD - 验证部署结果') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'server-root-ssh-pem',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    sh '''
                        ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no "$SSH_USER"@8.136.188.3 "
                            ls -la /var/www/html/jenkins-demo &&
                            cat /var/www/html/jenkins-demo/index.html
                        "
                    '''
                }
            }
        }
    }
}