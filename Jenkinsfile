pipeline {
    agent any
    tools {
    maven 'apache-maven-3.5.4'
    }
    stages {
        stage('Continuous Integration') {
        steps {
            sh 'mvn -Popenshift package'
            }
        }
        stage('Continous Delivery') {
        steps {
                sh '''
                    oc login https://52.184.24.45:8443 -u technet -p ${OPENSHIFT_PW} --insecure-skip-tls-verify=true
                    oc new-project coolstore
                    oc process -f src/main/openshift/template.json | oc create -f -
                    oc start-build coolstore --from-file=deployments/ROOT.war
                    '''
                }
            }
        }
    }
            