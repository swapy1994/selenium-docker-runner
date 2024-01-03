pipeline{

    agent any
    // here we are running jenkins job with parameters. to generate the below syntax go to 
    // go to your job >> pipeline syntax >> Declarative Directive Generator
    // select parameters:Parameters >> select choices:Choices
    // give your choices i.e. your parameters , also give the name of parameters ex. BROWSERS
    // click generate.
    parameters {
        choice choices: ['chrome', 'firefox'], description: 'Select the browser', name: 'BROWSER'
    }


    stages{

        stage('Start Grid'){
            steps{
               // use 'sh' instead of 'bat'  and use ${} instead of %% when executing on linux
               // to pass the jenkins parameters we need to use params class
                bat "docker-compose -f grid.yaml up --pull=always --scale ${params.BROWSER}=2 -d"
                
            }
        }

        stage('Run Tests'){
            steps{
                //here we have added alwasy option so that it will pull new updated image from dockerhub always.
                bat "docker-compose -f test-suites.yaml up --pull=always"
                script {
                    //here testng-failed.xml automatically gets generated if any test case fails.So we are using
                    // to check if the test execution failed or not and accordingly marking the stage as failed.
                    if(fileExists('output/flight-reservation/testng-failed.xml') || fileExists('output/vendor-portal/testng-failed.xml')){
                        error('some tests failed.')
                    }
                }
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