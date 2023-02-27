pipeline{
    agent any
    stages{
        stage("Echo"){
            steps{
                 echo "Husain"
            }
        }
        stage("Checkout")
        {
            steps{
                cleanWs()
                dir("Demo")
                {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Husain5253/Demo-trivy.git']])
                }
            }
            
        }
        stage("Docker Build")
        {
            steps{
                sh'''
                    cd Demo
                    export IMAGE=husain5253/demo:v1
                    sudo docker build -t $IMAGE .
                '''
            }
        }
        // stage("TRIVY SCAN")
        // {
        //     steps{
        //         withVault(configuration: [timeout: 60, vaultCredentialId: 'vault-token', vaultUrl: 'http://43.204.84.126:8200'], vaultSecrets: [[path: 'secret/dockerhub', secretValues: [[vaultKey: 'username'], [vaultKey: 'password']]]]) {
        //             sh'''
        //                 export IMAGE=husain5253/demo:v1
        //                 trivy image $IMAGE -u $username -p $password --severity MEDIUM,HIGH,CRITICAL -f json --output scan.json
        //                 cat scan.json
        //             '''
        //         }
               
        //         archiveArtifacts artifacts: '*.json', onlyIfSuccessful: true
        //     }
            
        // }
        stage("Docker Push")
        {
            steps{
                withVault(configuration: [timeout: 60, vaultCredentialId: 'vault-token', vaultUrl: 'http://43.204.84.126:8200'], vaultSecrets: [[path: 'secret/dockerhub', secretValues: [[vaultKey: 'username'], [vaultKey: 'password']]]]) {
                    sh'''
                        export IMAGE=husain5253/demo:v1
                        sudo docker login -u $username -p $password
                        sudo docker push $IMAGE
                    '''
                }
            }
        }
        
    }
    post{
        always{
                emailext to: "husainbalapurwala52@gmail.com",
                subject: "Image Artifacts",
                body: "Staus: ${currentBuild.currentResult} \nJob Name: ${env.JOB_NAME} \nMore Info can be found here: ${env.BUILD_URL}",
                attachmentsPattern: '*.json'
        }
    }
}
