pipeline{

    agent any

    stages{

        stage('Start Grid'){
            steps{
               // use 'sh' instead of 'bat' when executing on linux
                bat "docker-compose -f grid.yaml up -d"
                
            }
        }

        stage('Run Tests'){
            steps{
                bat "docker-compose -f test-suites.yaml up"
            }
        }
    }

    post {
        always {
            bat "docker-compose -f grid.yaml down"
            bat "docker-compose -f test-suites.yaml down"
            //archive the test result from project root directory. we use here reltive path of the file
            archiveArtifacts artifacts: 'output/flight-reservation/extent-reports/*.html', followSymlinks: false
            archiveArtifacts artifacts: 'output/vendor-portal/extent-reports/*.html', followSymlinks: false
        }
    }

}