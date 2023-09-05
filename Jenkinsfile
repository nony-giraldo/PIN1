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

     stage('Escaneo Trivy') {
            steps {
                // Ejecutar Trivy en un contenedor Docker
                script {
                    def imageToScan = '127.0.0.1:5000/ngiraldo/pin1'
                    def trivyCmd = "docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image --exit-code 0 --severity HIGH,MEDIUM,LOW $imageToScan"
                    def trivyResult = sh(script: trivyCmd, returnStatus: true)
                    
                    if (trivyResult != 0) {
                        currentBuild.result = 'FAILURE'
                        error('Se encontraron vulnerabilidades de seguridad en la imagen.')
                    }
                }
            }
        

     stage('Pass To K8s'){
        steps {
        sh '''
        sshpass -p 'master' ssh 172.17.0.1 -l root -o StrictHostKeyChecking=no "kubectl create deployment testapp --image=127.0.0.1:5000/ngiraldo/pin1"
       echo "Wait"
       sleep 10
       sshpass -p 'master' ssh 172.17.0.1 -l root -o StrictHostKeyChecking=no "kubectl expose deployment testapp --port=3000"
       sshpass -p 'master' ssh 172.17.0.1 -l root -o StrictHostKeyChecking=no "wget https://raw.githubusercontent.com/tercemundo/platzi-scripts-integracion/master/webapp/nodePort.yml"
       sshpass -p 'master' ssh 172.17.0.1 -l root -o StrictHostKeyChecking=no "kubectl apply -f nodePort.yml" 

           '''

            }
        }
    }
}

    
  

