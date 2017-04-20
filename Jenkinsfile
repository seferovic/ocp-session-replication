#!groovy

node {
    stage('Build') {
        build 'webapp-war'

        openshiftBuild apiURL: '', authToken: '', bldCfg: 'webapp-dev', checkForTriggeredDeployments: 'false', namespace: cicd, verbose: 'false'
        openshiftVerifyBuild apiURL: '', authToken: '', bldCfg: 'webapp-dev', checkForTriggeredDeployments: 'false', namespace: cicd, verbose: 'false'
    }
    stage('Deploy to Dev') {
        openshiftDeploy apiURL: '', authToken: '', depCfg: 'webapp-dev', namespace: cicd, verbose: 'false', waitTime: ''
        openshiftVerifyDeployment apiURL: '', authToken: '', depCfg: 'webapp-dev', namespace: cicd, replicaCount: '1', verbose: 'false', verifyReplicaCount: 'false', waitTime: ''

        openshiftTag alias: 'false', apiURL: '', authToken: '', destStream: 'webapp', destTag: 'qa', destinationAuthToken: '', destinationNamespace: cicd, namespace: cicd, srcStream: 'webapp', srcTag: 'latest', verbose: 'false'

    }
    stage('Approve QA Deployment') {
        timeout(time: 2, unit: 'DAYS') {
            input message: 'Do you want to deploy into Q&A?'
        }
    }
    // Publish to a QA environment
    stage('Deploy to QA') {
        openshiftDeploy apiURL: '', authToken: '', depCfg: 'webapp-qa', namespace: cicd, verbose: 'false', waitTime: ''
        openshiftVerifyDeployment apiURL: '', authToken: '', depCfg: 'webapp-qa', namespace: cicd, replicaCount: '1', verbose: 'false', verifyReplicaCount: 'false', waitTime: ''

        openshiftTag alias: 'false', apiURL: '', authToken: '', destStream: 'webapp', destTag: 'prod', destinationAuthToken: '', destinationNamespace: cicd, namespace: cicd, srcStream: 'webapp', srcTag: 'qa', verbose: 'false'
    }
    // Wait until authorization to push to production
    stage('Approve Production Deployment') {
        timeout(time: 2, unit: 'DAYS') {
            input message: 'Do you want to deploy into production?'
        }
    }
    // Push to production
    stage('Deploy to Production') {
        openshiftDeploy apiURL: '', authToken: '', depCfg: 'webapp-prod', namespace: cicd, verbose: 'false', waitTime: ''
        openshiftVerifyDeployment apiURL: '', authToken: '', depCfg: 'webapp-prod', namespace: cicd, replicaCount: '1', verbose: 'false', verifyReplicaCount: 'false', waitTime: ''
    }
}
