@Library("Shared") _
pipeline{
    agent {label "agent1"}
    
    triggers {
        GenericTrigger(
            genericVariables: [
                [key: 'event_type', value: '$.headers.X-GitHub-Event']
            ],
            causeString: 'Triggered on $event_type',
            token: 'my-secret-token',   // keep this secret
            printContributedVariables: true,
            printPostContent: true
        )
    }
    
    stages{
        stage("hello"){
            steps{
                script{
                    hello()
                }
            }
        }
        stage("code"){
            steps{
                script{
                    clone("https://github.com/deathnote995/django-notes-app.git", "main")
                }
            }
        }
        stage("build"){
            steps{
                script{
                    image_build("django-notes-app")
                }
            }
        }
        stage("image push"){
            steps{
                echo "Here pushing the image to docker hub"
                withCredentials([usernamePassword(credentialsId:"dockerHubCredential",usernameVariable:"dockerHubUser",passwordVariable:"dockerHubPass")]){
                    script{
                        image_push(env.dockerHubUser, env.dockerHubPass, "django-notes-app")
                    }
                    // sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    // sh "docker tag django-notes-app:latest ${env.dockerHubUser}/django-notes-app:latest"
                    // sh "docker push ${env.dockerHubUser}/django-notes-app:latest"
                }
                //echo "Image successfully pushed"
            }
        }
        stage("test"){
            steps{
                echo "Testing the build"
            }
        }
        stage("deploy"){
            steps{
                echo "Deploying the project"
                script{
                    deploy_build()
                }
                //sh "docker compose up -d"
            }
        }
    }
}
