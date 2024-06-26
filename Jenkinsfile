pipeline {
    agent any

    environment {
        // Set up environment variables if needed, e.g., AWS credentials
        AWS_ACCESS_KEY_ID = credentials('AKIAVRUVQLQVPTB6HJNU')
        AWS_SECRET_ACCESS_KEY = credentials('LlVp+GFnnkrrHaNTSzgkFjEMOesnCCtlvn8lZGh0')
        AWS_REGION = 'us-east-1'  // Set your desired AWS region
        STACK_NAME = 'fargate-stack'  // Desired name for your CloudFormation stack
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the repository
                git url: 'https://github.com/TreyMunnuh/fargzte.git', branch: 'main'
            }
        }

        stage('Validate CloudFormation Template') {
            steps {
                script {
                    // Assuming the CloudFormation template is located in the `cloudformation` directory
                    def templateFile = 'cloudformation/fargate.yaml'

                    // Validate the CloudFormation template
                    sh """
                    aws cloudformation validate-template --template-body file://${templateFile} --region ${AWS_REGION}
                    """
                }
            }
        }

        stage('Deploy Stack') {
            steps {
                script {
                    // Assuming the parameters file is located in the `cloudformation` directory
                    def parametersFile = 'cloudformation/parameters.yaml'

                    // Deploy or update the CloudFormation stack using the CloudFormation plugin
                    cloudFormation(
                        action: 'CreateOrUpdateStack',
                        stackName: STACK_NAME,
                        region: AWS_REGION,
                        templateFile: 'cloudformation/fargate.yaml',
                        parametersFile: 'cloudformation/parameters.yaml',
                        capabilities: ['CAPABILITY_IAM', 'CAPABILITY_NAMED_IAM']
                    )
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
