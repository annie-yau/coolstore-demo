pipeline {
    agent any
    tools {
    maven 'apache-maven-3.5.4'
    }
    // Define your secret project token here
    def project_token = 'abcdefghijklmnopqrstuvwxyz0123456789ABCDEF'

    // Reference the GitLab connection name from your Jenkins Global configuration (http://JENKINS_URL/configure, GitLab section)
    properties([
        gitLabConnection('your-gitlab-connection-name'),
        pipelineTriggers([
            [
                $class: 'GitLabPushTrigger',
                branchFilterType: 'All',
                triggerOnPush: true,
                triggerOnMergeRequest: false,
                triggerOpenMergeRequestOnPush: "never",
                triggerOnNoteRequest: true,
                noteRegex: "Jenkins please retry a build",
                skipWorkInProgressMergeRequest: true,
                secretToken: project_token,
                ciSkip: false,
                setBuildDescription: true,
                addNoteOnMergeRequest: true,
                addCiMessage: true,
                addVoteOnMergeRequest: true,
                acceptMergeRequestOnSuccess: false,
                branchFilterType: "NameBasedFilter",
                includeBranchesSpec: "release/qat",
                excludeBranchesSpec: "",
            ]
        ])
    ])
    
    stages {
        stage('Continuous Integration') {
        steps {
            sh 'mvn -Popenshift package'
            }
        }
        stage('Continous Delivery') {
        steps {
            withCredentials([string(credentialsId: 'OPENSHIFT_PW', variable: 'OPENSHIFT_PW')]) {
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
    }
            