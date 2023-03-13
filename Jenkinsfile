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
    if (env.BRANCH_NAME == "main") {
      echo "I am the ${env.BRANCH_NAME} branch"
      stage('Build a gradle project') {
        git branch: 'main', url: 'https://github.com/umlDevOps/week7.git'
        container('gradle') {
          stage('Build a gradle project') {
            sh '''
            cd Chapter08/sample1
            chmod +x gradlew
            ./gradlew build
            '''
          }
        }
      }
      stage("Code coverage") {
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

                    publishHTML (target: [
                        reportDir: 'Chapter08/sample1/build/reports/tests/test',
                        reportFiles: 'index.html',
                        reportName: "JaCoCo Report"
                    ])                       
                }

      stage("jacoco checkstyle") {
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
      sh '''
      mv ./build/libs/calculator-1.0-SNAPSHOT.jar /mnt
      '''
    }

    stage('Build Java Image') {
      container('kaniko') {
        stage('Build a gradle project') {
          sh '''
          echo 'FROM openjdk:8-jre' > Dockerfile
          echo 'COPY ./calculator-1.0-SNAPSHOT.jar app.jar' >> Dockerfile
          echo 'ENTRYPOINT ["java", "-jar", "app.jar"]' >> Dockerfile
          mv /mnt/calculator-1.0-SNAPSHOT.jar .
          /kaniko/executor --context `pwd` --destination umldevops23/calculator-kaniko:1.0
          '''
        }
      }
    }

  if (env.BRANCH_NAME == "feature") {
      echo "I am the ${env.BRANCH_NAME} branch"
      stage('Build a gradle project') {
        git branch: 'main', url: 'https://github.com/umlDevOps/week7.git'
        container('gradle') {
          stage('Build a gradle project') {
            sh '''
            cd Chapter08/sample1
            chmod +x gradlew
            ./gradlew build
            '''
          }
        }
      }
      /*stage("Code coverage") {
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

                    publishHTML (target: [
                        reportDir: 'Chapter08/sample1/build/reports/tests/test',
                        reportFiles: 'index.html',
                        reportName: "JaCoCo Report"
                    ])                       
                }*/

      stage("jacoco checkstyle") {
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
      sh '''
      mv ./build/libs/calculator-feature-0.1-SNAPSHOT.jar /mnt
      '''
    }

    stage('Build Java Image') {
      container('kaniko') {
        stage('Build a gradle project') {
          sh '''
          echo 'FROM openjdk:8-jre' > Dockerfile
          echo 'COPY ./calculator-feature-0.1-SNAPSHOT.jar app.jar' >> Dockerfile
          echo 'ENTRYPOINT ["java", "-jar", "app.jar"]' >> Dockerfile
          mv /mnt/calculator-0.1-SNAPSHOT.jar .
          /kaniko/executor --context `pwd` --destination umldevops23/calculator-feature-kaniko:0.1
          '''
        }
      }
    }
  }

  if (env.BRANCH_NAME == "playground") {
      echo "I am the ${env.BRANCH_NAME} branch"
      stage('Build a gradle project') {
        git branch: 'main', url: 'https://github.com/umlDevOps/week7.git'
        container('gradle') {
          stage('Build a gradle project') {
            sh '''
            cd Chapter08/sample1
            chmod +x gradlew
            ./gradlew build
            '''
          }
        }
      }
    }

}