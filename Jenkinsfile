pipeline {
    agent any

    parameters {
        string(name: 'REPORT_PATH', defaultValue: 'C:\\Program Files\\apache-jmeter-5.6.3\\bin\\sri', description: 'Report Output Path')
        string(name: 'JMETER_PATH', defaultValue: 'C:\\apache-jmeter-5.6.3\\bin', description: 'JMeter Installation Path')
        string(name: 'TEST_PLAN', defaultValue: 'C:\\Program Files\\apache-jmeter-5.6.3\\bin\\TestPlan.jmx', description: 'JMeter Test Plan Path')
        string(name: 'RESULTS_FILE', defaultValue: 'C:\\Program Files\\apache-jmeter-5.6.3\\bin\\results.jtl', description: 'JMeter Results File Path')
    }

    stages {
        stage('Checkout Repository') {
            steps {
                script {
                    git clone https://github.com/sreekanthops/perf.git
                }
            }
        }
        stage('Run JMeter Test') {
            steps {
                script {
                    def reportPath = params.REPORT_PATH
                    def jmeterPath = params.JMETER_PATH
                    def testPlan = params.TEST_PLAN
                    def resultsFile = params.RESULTS_FILE

                    // Get current date-time for folder naming
                    def dateTime = new Date().format('yyyy-MM-dd_HH-mm-ss')
                    def newReportOutput = "${reportPath}-${dateTime}"

                    // Set JAVA_HOME and PATH if necessary
                    withEnv(['JAVA_HOME=C:\\Program Files\\Java\\jdk-21', 'PATH+JAVA_HOME=C:\\Program Files\\Java\\jdk-21\\bin']) {
                        stage('Preparation') {
                            echo "Cleaning up previous results..."
                            bat "del /f /q \"${resultsFile}\""

                            // Check if the report output folder exists, and create a new one with timestamp if it does
                            bat """
                                if exist "${reportPath}" (
                                    echo Folder exists, creating new folder with timestamp: ${newReportOutput}
                                    mkdir "${newReportOutput}"
                                ) else (
                                    echo Folder does not exist, creating new folder: ${newReportOutput}
                                    mkdir "${newReportOutput}"
                                )
                            """
                        }

                        stage('Run JMeter Test') {
                            echo "Running JMeter test..."
                            bat """
                                dir %WORKSPACE%
                                cd ${jmeterPath}
                                jmeter -n -t "${testPlan}" -l "${resultsFile}" -e -o "${newReportOutput}"
                            """
                        }

                        stage('Post-Processing') {
                            echo "Post-processing results..."
                            archiveArtifacts artifacts: '**/results.jtl', allowEmptyArchive: true
                        }
                    }
                }
            }
        }
    }
}
