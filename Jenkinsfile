pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'maven:3.8.8-eclipse-temurin-17'
                }
            }
            steps {
                sh 'mvn clean package -DskipTests -B -ntp'
            }
        }
        // stage('Deploy with jboss-cli') {
        //     agent any
        //     environment {
        //         AMAZON_LINUX_IP = '35.89.125.148'
        //         JBOSS_CREDENTIALS = credentials('jboss-credentials')
        //     }            
        //     steps {
        //         sshagent (credentials: ['amazon-linux-private-key']) {
        //             sh '''
        //                 scp -o StrictHostKeyChecking=no target/applicationPetstore.war ec2-user@$AMAZON_LINUX_IP:/home/ec2-user
                        
        //                 ssh ec2-user@$AMAZON_LINUX_IP "~/jboss-eap-7.4/bin/jboss-cli.sh --user=$JBOSS_CREDENTIALS_USR --password=$JBOSS_CREDENTIALS_PSW -c --command='undeploy applicationPetstore.war'"
                        
        //                 ssh ec2-user@$AMAZON_LINUX_IP "~/jboss-eap-7.4/bin/jboss-cli.sh --user=$JBOSS_CREDENTIALS_USR --password=$JBOSS_CREDENTIALS_PSW -c --command='deploy /home/ec2-user/applicationPetstore.war'"
                        
        //                 ssh ec2-user@$AMAZON_LINUX_IP 'rm -f /home/ec2-user/applicationPetstore.war'
        //             '''
        //         }
        //     }
        // }
        stage('Deploy with Ansible') {
            agent {
                docker {
                    image 'alpine/ansible:2.18.1'
                    args '-u root'
                }
            }
            environment {
                ANSIBLE_HOST_KEY_CHECKING = "False"
                JBOSS_CREDENTIALS = credentials('jboss-credentials')
            }
            options { skipDefaultCheckout() }
            steps {
                dir('ansible'){
                    sshagent (credentials: ['amazon-linux-private-key']){
                        sh 'env | sort'
                        sh 'ansible --version'
                        sh 'ansible-galaxy --version'
                        // sh 'ansible-galaxy collection install community.general'
                        sh 'ansible-playbook -i hosts deploy_jboss.yml'
                    }
                }
            }
        }       
    }    
}
