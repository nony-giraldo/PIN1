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

     stage('Pass To K8s'){
        steps {
        sh '''
        kubectl create deployment testapp --image=127.0.0.1:5000/ngiraldo/pin1
        echo "Wait"
        sleep 10
        kubectl expose deployment testapp --port=3000
        wget https://github.com/nony-giraldo/PIN1/blob/master/nodePort.yml
        kubectl apply -f nodePort.yml"

           '''

            }
        }
    }
}

    
  

