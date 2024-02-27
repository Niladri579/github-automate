Jpipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Niladri579/github-automate.git'
            }
        }

        stage('Terraform Apply') {
            steps {
                sh 'terraform init'
                sh 'terraform apply -auto-approve'
            }
        }

        stage('Setup SSH Connection') {
            steps {
                script {
                    // Obtain the IP addresses of the newly created instances
                    def ipAddresses = sh(script: 'terraform output -json', returnStdout: true).trim()
                    ipAddresses = readJSON text: ipAddresses

                    // Setup SSH connection with the newly created instances
                    for (ip in ipAddresses) {
                        sshagent(['your-ssh-credentials-id']) {
                            sh "ssh -o StrictHostKeyChecking=no ec2-user@${ip} uptime"
                        }
                    }
                }
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                // Run Ansible playbook to install nginx on all instances
                ansiblePlaybook(
                    playbook: 'path/to/ansible/playbook.yml',
                    inventory: 'path/to/ansible/inventory',
                    credentialsId: 'your-ansible-credentials-id'
                )
            }
        }
    }

    post {
        always {
            // Cleanup: Destroy AWS instances after completion
            stage('Terraform Destroy') {
                steps {
                    sh 'terraform destroy -auto-approve'
                }
            }
        }
    }
}
