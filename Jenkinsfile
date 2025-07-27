pipeline{
    agent any 

    environment{
        EC2_HOST = "13.203.105.66" // IP of ec2 we want to deploy ons
        SSH_CREDENTIAL_ID = "ec2-key" // .pem file content id
        REMOTE_USER = "ubuntu" // username of ec2 machine
        REMOTE_PATH = "/home/ubuntu/app"
        WEB_ROOT = "/var/www/html"
        SERVER = "nginx" // name of the server
    }

    stages{
        stage("Build"){
            steps{
                echo "Installing dependencies"
                sh "npm install"
                echo "Building the app"
                sh "npm run build"
                echo "Build completed"
            }
        }
        stage("Deploy"){
            steps{
                echo "Starting Deployment"
                sshagent(credentials: [env.SSH_CREDENTIAL_ID]){
                    sh """
                        echo "Creating remote directory"
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${EC2_HOST} "mkdir -p ${REMOTE_PATH}"
                       
                        echo "copying distribution to remote path"
                        scp -o StrictHostKeyChecking=no -r dist/* ${REMOTE_USER}@${EC2_HOST}:${REMOTE_PATH}/

                        echo "Restarting nginx"
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${EC2_HOST}'
                        sudo rm -rf ${WEB_ROOT}/*
                        sudo cp -r ${REMOTE_PATH}/* ${WEB_ROOT}/
                        sudo systemctl restart ${SERVER}
                        ' 
                    """
                }
            }
        }
    }
}