# Final Project Assessment for Scalefocus Academy

*Deploy a WordPress on Kubernetes (using Minicube) with Helm and automation with Jenkins*  

1. Create a repository for the project.  
![repo](./assets/1.png)  
![repo2](./assets/2.png)  

2. Download the Helm chart, unzip and put it in the dir.  
   ```bash
    curl -LO https://github.com/bitnami/charts/archive/refs/heads/main.zip
   ```  
   ![repo with helm chart](./assets/3.png)  

3. Change the following in *bitnami/wordpress/values.yml*  
   ```yml
   type: LoadBalancer
   
   # into
   
   type: ClusterIP
   ```

4. Install Kubernetes plugins and Create Jenkins Pipeline  
![jenkins pipeline](./assets/jenkins-pipeline-setup.png)  

5. Create a Jenkinsfile in the repo and push the changes.
   ```Jenkinsfile
   pipeline {
      agent any

      environment {
         KUBECONFIG = '/var/lib/jenkins/.kube/config'
      }

      stages {
         stage('Namespace') {
               steps {
                  script {
                     def namespaceExists = sh(script: 'kubectl get namespace wp', returnStatus: true) == 0
                     if (namespaceExists) {
                           echo 'Namespace wp exists'
                           return
                     } else {
                           echo 'Creating wp namespace'
                           sh 'kubectl create namespace wp'
                     }
                  }
               }
         }

         stage('Helm') {
               steps {
                  script {
                     sh 'helm dependency build ./bitnami/wordpress'
                     sh 'helm upgrade --install final-project-wp-scalefocus ./bitnami/wordpress -n wp'
                  }
               }
         }
      }
   }
   ```

6. Trigger build from Jenkins
![jenkinsbuild](./assets/jenkins-build.png)
![jenkinsbuildoutput](./assets/jenkins-build-output.png)

7. Minikube status
![minikubestatus](./assets/minikubestatus.png)

8. Run 
   ```bash
   kubectl port-forward --namespace wp svc/final-project-wp-scalefocus-wordpress 9000:80
   ```  
   ![portforward](./assets/kubectl-port-forward.png)  

9. Wordpress
![hello world](./assets/hello%20world.png)  
![wp-admin](./assets/wp-admin.png)  
![user-pass](./assets/user-pass.png)  
![blogpost](./assets/blogpost.png)