pipeline {
    agent any
    environment {
        SWARM_SERVICE_NAME = 'app_elastic'
    }
    stages {
        stage('Build with unit testing') {
            steps {
                sh """
                docker build -t ${env.SWARM_SERVICE_NAME}:${env.GIT_COMMIT} .
                """
            }
        }  

        stage('Update TEST swarm') {
            steps {
                sh """
                docker service update \
                -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" \
                --replicas 1 \
                --update-delay 10s \
                --env-add BUILD_NUMBER=${env.BUILD_NUMBER} \
                --env-add EXECUTE_SPACE=test \
                --image ${env.SWARM_SERVICE_NAME}:${env.GIT_COMMIT} \
                test_${env.SWARM_SERVICE_NAME}
                """
            }
        }    

        stage('Update PROD swarm') {
            steps {
                sh """
                docker service update \
                -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" \
                --replicas 1 \
                --update-delay 10s \
                --env-add BUILD_NUMBER=${env.BUILD_NUMBER} \
                --env-add EXECUTE_SPACE=prod \
                --image ${env.SWARM_SERVICE_NAME}:${env.GIT_COMMIT} \
                prod_${env.SWARM_SERVICE_NAME}
                """
            }
        }    
    }
}