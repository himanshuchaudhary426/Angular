pipeline{
	agent any
	environment{
		registry = 'himanshuchaudhary/angular-app'
	}
	stages{
		stage('initialize npm'){
			steps{
				sh "echo -------INIT----------"
				sh "npm install"
				sh "npm install @angular/cli"
				sh "echo -------INIT Successfull------"
				}
			}
		}

		stage('build'){
			steps{
				sh "echo --------building----------"
				sh "npm run build --prod"
			}
		}

		stage('build-image'){
			when{
				branch 'master'
			}
			steps{
				sh 'docker build -t $registry:$BUILD_NUMBER .'
                echo "Image Build successfull"
                withDockerRegistry([ credentialsId: "docker", url: "" ]) {
                    sh 'docker push $registry:$BUILD_NUMBER'
				}
				echo "Image pushed successfully"
			}
		}
		stage('deploy to production'){
			when{
				branch 'master'
			}
			steps{
				withKubeConfig(
            		clusterName: 'gke_resounding-sled-291408_us-central1-c_cluster-1', contextName: 'gke_resounding-sled-291408_us-central1-c_cluster-1', credentialsId: 'kube-config', namespace: 'capstone') {
            			sh "cat Deployment.yml | sed 's/angular-app:v3/angular-app:$BUILD_NUMBER/' | kubectl apply -f -"
				}
			}
		}
	}	
	post{
		always{
			emailext ( 
				subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!!', 
				body: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS:Check console output at $BUILD_URL to view the results.',
				to : 'himanshuchaudhary426@gmail.com',
				attachLog: true
				)
		}
		success{
			cleanWs()
		}
		failure{
			sh "echo failed"
		}
	}
}