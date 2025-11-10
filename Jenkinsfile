pipeline {
    agent any

    environment {
        TF_DIR = 'iac'
        AWS_REGION = 'us-east-1'
        TF_VAR_vpc_name = 'acit-vpc'
    }

    stages {
        stage('Setup Terraform') {
            steps {
                sh '''
                    apt-get update -y
                    apt-get install -y unzip wget curl
                    wget https://releases.hashicorp.com/terraform/1.6.5/terraform_1.6.5_linux_amd64.zip
                    unzip terraform_1.6.5_linux_amd64.zip
                    mv terraform /usr/local/bin/
                    terraform -version
                '''
            }
        }

        stage('Init Terraform') {
            steps {
                sh '''
                    mkdir -p ${TF_DIR}
                    cat > ${TF_DIR}/main.tf <<'EOF'
provider "aws" {
  region = "${AWS_REGION}"
}
# (rest of Terraform code here)
EOF
                '''
            }
        }

        stage('Terraform Init') {
            steps {
                sh '''
                    cd ${TF_DIR}
                    terraform init
                '''
            }
        }

        stage('Terraform Plan') {
            steps {
                sh '''
                    cd ${TF_DIR}
                    terraform plan -out=tfplan
                '''
            }
        }

        stage('Terraform Apply') {
            steps {
                input message: 'Approve to create VPC?'
                sh '''
                    cd ${TF_DIR}
                    terraform apply -auto-approve tfplan
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Custom VPC 'acit-vpc' created successfully!"
        }
        failure {
            echo "❌ VPC creation failed!"
        }
    }
}
