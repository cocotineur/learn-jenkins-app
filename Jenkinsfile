pipeline {
    agent any

    stages {
        /*stage('Build') {
            // install node to use npm command
           agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci 
                    npm run build
                    ls -la
                '''
            }
        }*/
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    test -f build/index.html 
                    npm test
                '''

            }
        }

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.45.1-jammy'
                    reuseNode true
                    args '-u root:root'
                }
            }
            steps {
                //  node_modules/.bin/serve -s build starts a webserver that always runs in jenkins so thet the job never finishs
                // To solve this problem we can start the server in background (&) and not block the excution of the rest of the commands. 
                // sleep to not immediately run the next command after server is going to start. We have to wait some tine umtil the server is started befor we run the next commands 
                sh '''
                    npm install serve
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npx playwright test
                '''

            }
        }
    }

    post {
            always {
                junit 'test-results/junit.xml'
            }
    }
}
