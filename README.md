#KUBE COMMAND
kubectl create namespace {namespace}
kubectl get namespace
kubectl create -f {file component}
kubectl delete -f {file component}
kubectl create configmap {name} -n {namespace} --from-file ./application.yaml
kubectl -n {namespace} get pod
kubectl -n {namespace} get service
kubectl -n {namespace} get configmap
kubectl -n {namespace} get deployment
kubectl -n {namespace} describe {type} {name}
kubectl -n {namespace} logs -f {pod name}
kubectl -n {namespace} get events --sort-by='.lastTimestamp'
kubectl -n {namespace} exec -it {pod name} bash
kubectl -n {namespace} scale --replicas=1 deploy {deployment name}

root@minikube:/# cat etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
192.168.49.2	minikube
192.168.65.2	host.minikube.internal
192.168.49.2	control-plane.minikube.internal

#brew install helm
helm create {chart name}  CREATE FOLDER CHART
helm -n {namespace} install {helm name} {folder helm}
helm -n {namespace} uninstall {helm name}
helm -n {namespace} list

#install aws cli
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" 

sudo apt install unzip

sudo unzip awscliv2.zip  

sudo ./aws/install

aws --version

#install helm 
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
sudo chmod 700 get_helm.sh
sudo ./get_helm.sh
helm version --client
#install kube
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo touch /etc/apt/sources.list.d/kubernetes.list
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
#install eks
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin

#Docker Jenkins Integration
Add jenkins user to Docker group
sudo usermod -a -G docker jenkins

Restart Jenkins service
sudo service jenkins restart

Reload system daemon files
sudo systemctl daemon-reload

Restart Docker service as well

sudo service docker stop
sudo service docker start



pipeline {
   
   tools {
        maven 'Maven3'
    }
    agent any
    environment {
        registry = "ECR_URL"
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                // clone git    
            }
        }
      stage ('Build') {
          steps {
            sh 'mvn clean install'           
            }
      }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry 
          dockerImage.tag("$BUILD_NUMBER")
        }
      }
    }
   
    stage('Pushing to ECR') {
     steps{  
         script {
                sh 'connect ect '
                sh 'docker push name_ecr'
         }
        }
      }
        stage ('Helm Deploy') {
          steps {
            script {
                sh "helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                }
            }
        }
    }
}

 

