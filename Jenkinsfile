pipeline {
  agent any

  options {
    timeout(time: 2, unit: 'MINUTES')
  }

  environment {
    ARTIFACT_ID = "elbuo8/webapp:${env.BUILD_NUMBER}"
  }
   stages {
   stage('Building image') {
      steps{
          sh '''
          docker build -t testapp .
             '''  
        }
    }
  
  
    stage('Run tests') {
      steps {
        sh "docker run testapp npm test"
      }
    }
   stage('Deploy Image') {
      steps{
        sh '''
        docker tag testapp 127.0.0.1:5000/ngiraldo/pin1
        docker push 127.0.0.1:5000/ngiraldo/pin1   
        '''
        }
      }

     /*stage('Vulnerability Scan - Docker ') {
        steps {
          sh "docker run --rm  -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image --severity=CRITICAL 127.0.0.1:5000/ngiraldo/pin1"
            }
        }*/
        

     stage('Pass To K8s'){
        steps {
        sh '''
        minikube kubectl -- create deployment testapp --image=127.0.0.1:5000/ngiraldo/pin1
       echo "Wait"
       sleep 10
       minikube kubectl -- expose deployment testapp --port=3000
       wget https://raw.githubusercontent.com/tercemundo/platzi-scripts-integracion/master/webapp/nodePort.yml
       minikube kubectl -- apply -f nodePort.yml

           '''

            }
        }
    }
}

    
  

