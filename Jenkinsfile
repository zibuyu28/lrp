pipeline {
    agent any
    stages {
        stage('hello pipeline') {
            steps {
                echo 'Hello pipeline!'
                sh 'rm -rf x1-node'
                sh 'rm -rf x1-contracts'
                sh 'rm -rf x1-data-availability'
            }
        }
        stage('build node image') {
            steps {
                echo ${node_branch}
                sh 'docker rmi x1-node'
                sh 'git clone -b ${node_branch} https://github.com/okx/x1-node.git'
                sh 'cd ./x1-node'
                sh 'make build-docker'
                sh 'cd -'
            }
        }
        stage('build dac image') {
            steps {
                sh 'echo ${dac_branch}''
                sh 'docker rmi x1-data-availability'
                sh 'git clone -b ${dac_branch} https://github.com/okx/x1-data-availability.git'
                sh 'cd x1-data-availability'
                sh 'make build-docker'
                sh 'cd -'
            }
        }
        stage('contract') {
            steps {
                sh 'echo ${contract_branch}'
                sh 'git clone -b ${contract_branch} https://github.com/okx/x1-contracts.git'
            }
        }
        stage('reset contract env') {
            steps {
                sh 'cd ./x1-contracts'
                sh 'rm deployment/deploy_output.json'
                sh 'git reset --hard'
                sh 'cp ../zkevm-docker/config/deploy_parameters.json deployment/deploy_parameters.json'
                sh 'cp ../zkevm-docker/config/contract_env_example .env'
                // 替换 paris
                sh 'sed -i hardhat.config.js'
                sh 'cp ../zkevm-docker/config/1_createGenesis.js deployment/1_createGenesis.js'
                sh 'cd -'
            }
        }
        stage('setup db') {
            steps {
                sh 'docker-compose up -d x1-state-db'
                sh 'docker-compose up -d x1-pool-db'
                sh 'docker-compose up -d x1-event-db'
                sh 'docker-compose up -d x1-bridge-db'
                sh 'docker-compose up -d x1-bridge-redis'
                sh 'docker-compose up -d x1-data-availability-db'
                sh 'docker-compose up -d x1-mock-l1-network'
            }
        }
    }
}
