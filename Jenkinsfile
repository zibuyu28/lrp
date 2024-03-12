pipeline {
    agent any
    stages {
        stage('hello pipeline') {
            steps {
                echo 'Hello pipeline!'
                rm -rf x1-node
                rm -rf x1-contracts
                rm -rf x1-data-availability
            }
        }
        stage('build node image') {
            steps {
                echo ${node_branch}
                docker rmi x1-node
                git clone -b ${node_branch} https://github.com/okx/x1-node.git
                cd ./x1-node
                make build-docker
                cd -
            }
        }
        stage('build dac image') {
            steps {
                echo ${dac_branch}
                docker rmi x1-data-availability
                git clone -b ${dac_branch} https://github.com/okx/x1-data-availability.git
                cd x1-data-availability
                make build-docker
                cd -
            }
        }
        stage('contract') {
            steps {
                echo ${contract_branch}
                git clone -b ${contract_branch} https://github.com/okx/x1-contracts.git
            }
        }
        stage('reset contract env') {
            steps {
                cd ./x1-contracts
                rm deployment/deploy_output.json
                git reset --hard
                cp ../zkevm-docker/config/deploy_parameters.json deployment/deploy_parameters.json
                cp ../zkevm-docker/config/contract_env_example .env
                // 替换 paris
                sed -i hardhat.config.js
                cp ../zkevm-docker/config/1_createGenesis.js deployment/1_createGenesis.js
                cd -
            }
        }
        stage('setup db') {
            steps {
                docker-compose up -d x1-state-db
                docker-compose up -d x1-pool-db
                docker-compose up -d x1-event-db
                docker-compose up -d x1-bridge-db
                docker-compose up -d x1-bridge-redis
                docker-compose up -d x1-data-availability-db
                docker-compose up -d x1-mock-l1-network
            }
        }
    }
}
