pipeline {
   agent any
    tools {
	  jdk 'Java8'
      maven "Maven3.3.9"
   }
    stages
   {
   stage('git clone') {
         steps {
            // Get some code from a GitHub repository
            git 'https://github.com/Shilpavsqe/game.git'
        }  
      }
	stage ('Compile and Build') {
         steps {
           sh 'mvn clean install -U  -Dmaven.test.skip=true'
         }
	}
    stage ('Publishing Artifact') {
steps {
	nexusArtifactUploader artifacts: [[artifactId:'gameoflife1', classifier: '', file: '/var/lib/jenkins/workspace/pipeline/gameoflife-build/target/gameoflife-build-1.0-SNAPSHOT.jar', type:'jar', type: 'jar']], credentialsId: '8f725766-a4c3-4988-b3f2-6108746bb19a', groupId: 'com.wakaleo.gameoflife', nexusUrl: 'http://10.128.0.2:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'release', version: '4.0.0'
           archiveArtifacts '**/*.jar'
	}
     }
    stage('Docker Build') {
      agent any
      steps {
        sh '''
	docker build -t pudirohith/game:v1 /var/lib/jenkins/workspace/pipeline/
	docker tag pudirohith/game:v1 gcr.io/polished-triode-325915/game-of-life-1:v1
	'''
      }
    }
    stage ('Publish Docker Image'){
        steps {
          sh '''
          docker push gcr.io/polished-triode-325915/game-of-life-1:v1
          '''
        }
      }
    stage ('Deploy to kubernetes'){
        steps{
          script {

            sh "kubectl config use-context gke_polished-triode-325915_us-central1-a_cluster-1"
		       // sh "export PATH=/home/jenkins/:$PATH"
			      sh "cd ${WORKSPACE}"
		  	    //  sh "kubectl delete -f '${WORKSPACE}'/kube/gcloud-deployment.yml"
			      sh "kubectl create -f '${WORKSPACE}'/kube/gcloud-service.yml"
			      sh "kubectl create -f '${WORKSPACE}'/kube/gcloud-deployment.yml"

            }
       }
    }
   }
}
