pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('Code checkout from GitHub') {
            steps {
                script {
                    cleanWs()
                    git credentialsId: 'michalzietkowski_pat', url: 'https://github.com/michalzietkowski/abcd-student', branch: 'main'
                }
            }
        }
        stage('Prepare') {
            steps {
                sh 'mkdir -p results/'
            }
        }
//         stage('DAST') {
//             steps {
//                 sh '''
//                     docker run --name juice-shop -d --rm \
//                     -p 3000:3000 bkimminich/juice-shop
//                     sleep 10
//                 '''
//                 sh '''
//                     docker run --name zap \
//                     --add-host=host.docker.internal:host-gateway \
//                     -v /home/michal/devsecops/abcd-student/.zap:/zap/wrk/:rw \
//                     -t ghcr.io/zaproxy/zaproxy:stable bash -c \
//                     "zap.sh -cmd -addonupdate; zap.sh -cmd -addoninstall communityScripts -addoninstall pscanrulesAlpha -addoninstall pscanrulesBeta -autorun /zap/wrk/passive.yaml" || true
//                 '''
//             }
//             post {
//                 always {
//                     sh '''
//                         docker cp zap:/zap/wrk/reports/zap_html_report.html ${WORKSPACE}/results/zap_html_report.html
//                         docker cp zap:/zap/wrk/reports/zap_xml_report.xml ${WORKSPACE}/results/zap_xml_report.xml
//                         docker stop zap juice-shop
//                         docker rm zap
//                     '''
//                 }
//             }
//         stage('OSV scanner') {
//             steps {
//                 sh 'mkdir -p results/'
//                 sh 'osv-scanner scan --lockfile package-lock.json --format json --output results/osv_report.json || true'
//             }
//         }
//         stage('TruffleHog') {
//             steps {
//                 sh 'mkdir -p results/'
//                 sh 'trufflehog git file://. --only-verified --json > results/truffle_hog_report.json'
//             }
//         }
        stage('Semgrep') {
            steps {
                sh 'mkdir -p results/'
                sh 'semgrep scan --config auto --json > results/semgrep_report.json'
            }
        }

    }
    post {
        always {
            echo 'Archiving results...'
            archiveArtifacts artifacts: 'results/**/*', fingerprint: true, allowEmptyArchive: true
            echo 'Sending reports to DefectDojo...'
//             defectDojoPublisher(artifact: 'results/zap_xml_report.xml', productName: 'Juice Shop', scanType: 'ZAP Scan', engagementName: 'michalzietkowski@gmail.com')
//             defectDojoPublisher(artifact: 'results/osv_report.json', productName: 'Juice Shop', scanType: 'OSV Scan', engagementName: 'michalzietkowski@gmail.com')
            defectDojoPublisher(artifact: 'results/semgrep_report.json', productName: 'Juice Shop', scanType: 'Semgrep JSON Report', engagementName: 'michalzietkowski@gmail.com')
//             defectDojoPublisher(artifact: 'results/truffle_hog_report.json', productName: 'Juice Shop', scanType: 'Trufflehog Scan', engagementName: 'michalzietkowski@gmail.com')
       }
    }
}