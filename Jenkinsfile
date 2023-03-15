podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: gradle
        image: gradle:6.3-jdk14
        command:
        - sleep
        args:
        - 99d
        volumeMounts:
        - name: shared-storage
          mountPath: /mnt        
      - name: kaniko
        image: gcr.io/kaniko-project/executor:debug
        command:
        - sleep
        args:
        - 9999999
        volumeMounts:
        - name: shared-storage
          mountPath: /mnt
        - name: kaniko-secret
          mountPath: /kaniko/.docker
      restartPolicy: Never
      volumes:
      - name: shared-storage
        persistentVolumeClaim:
          claimName: jenkins-pv-claim
      - name: kaniko-secret
        secret:
            secretName: dockercred
            items:
            - key: .dockerconfigjson
              path: config.json
''') {
    node(POD_LABEL) {
        stage('Run pipeline against a gradle project') {
           container('gradle') {
                stage('Build a gradle project') {
                    // from the git plugin
                    // https://www.jenkins.io/doc/pipeline/steps/git/
                    git 'https://github.com/umlDevOps/Continuous-Delivery-with-Docker-and-Jenkins-Second-Edition.git'
                    sh '''
                    cd Chapter08/sample1
                    chmod +x gradlew
                    ./gradlew build
      
                    '''
                }
            
                stage("Code coverage") {
                  if (env.BRANCH_NAME=="main"){
                    echo "I am the ${env.BRANCH_NAME} branch"
                    try {
                        sh '''
        	            pwd
               		    cd Chapter08/sample1
                	    ./gradlew jacocoTestCoverageVerification
                        ./gradlew jacocoTestReport
                        '''
                    } catch (Exception E) {
                        echo 'Failure detected'
                    }

                    // from the HTML publisher plugin
                    // https://www.jenkins.io/doc/pipeline/steps/htmlpublisher/
                    publishHTML (target: [
                        reportDir: 'Chapter08/sample1/build/reports/tests/test',
                        reportFiles: 'index.html',
                        reportName: "JaCoCo Report"
                    ])                       
                  }
                }

                stage("jacoco checkstyle") {
                  if (env.BRANCH_NAME=="main" || env.BRANCH_NAME=="feature"){
                    echo "I am the ${env.BRANCH_NAME} branch"
                    try {
                        sh '''
                        pwd
               		    cd Chapter08/sample1
                        ./gradlew checkstyleMain
                        '''
                    } catch (Exception E) {
                        echo "Failure detected"
                    }

                    publishHTML (target: [
                        reportDir: 'Chapter08/sample1/build/reports/checkstyle/',
                        reportFiles: 'main.html',
                        reportName: 'jacoco checkstyle Report'
                    ])
                  }
                }
                
           }
        }
    }

}