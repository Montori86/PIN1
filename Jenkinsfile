pipeline {
  agent any

  options {
    timeout(time: 2, unit: 'MINUTES')
  }

  environment {
    ARTIFACT_ID = "elbuo8/webapp:${env.BUILD_NUMBER}"
  }

  stages {
    stage('Start Docker Registry') {
      steps {
        script {
          // Inicia el registro en el puerto 5000 si no está corriendo
          sh '''
          if [ ! "$(docker ps -q -f name=registry)" ]; then
              if [ "$(docker ps -aq -f status=exited -f name=registry)" ]; then
                  # Si el contenedor existe pero está detenido, lo iniciamos
                  docker start registry
              else
                  # Si no existe, creamos uno nuevo
                  docker run -d -p 5000:5000 --restart=always --name registry registry:2
              fi
          fi
          '''
        }
      }
    }

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
        docker tag testapp 127.0.0.1:5000/mguazzardo/testapp
        docker push 127.0.0.1:5000/mguazzardo/testapp   
        '''
      }
    }
  }
}



    
  

