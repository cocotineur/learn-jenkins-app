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
                    reuseNode true // -> to synchronize docker workspace and jenkins agent workspace so that it used he same location
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
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                    //args '-u root:root' // --> please not used it because the folder test-results from 'npx playwright test' in the workspace will be markes as root and cannot be e.g cleaned up/removed by jenkins if needed
                }
            }
            steps {
                /*   node_modules/.bin/serve -s build starts a webserver that always runs in jenkins so thet the job never finishs
                To solve this problem we can start the server in background (&) and not block the excution of the rest of the commands. 
                sleep to not immediately run the next command after server is going to start. We have to wait some tine umtil the server is started before we run the next commands */
                /* npm install serve -> prefer local installation instead of global (-g) . with local installation serve command will be set into node_module/.bin
                 */
                sh '''
                    npm install serve 
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npx playwright test --reporter=html
                '''

            }
        }
    }

    post {
            always {
                junit 'jest-results/junit.xml'
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Plawright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
            }
    }
}
