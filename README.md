#KUBE COMMAND
kubectl create namespace {namespace} <br>
kubectl get namespace <br>
kubectl create -f {file component} <br>
kubectl delete -f {file component} <br>
kubectl create configmap {name} -n {namespace} --from-file ./application.yaml <br>
kubectl -n {namespace} get pod <br>
kubectl -n {namespace} get service <br>
kubectl -n {namespace} get configmap <br>
kubectl -n {namespace} get deployment <br>
kubectl -n {namespace} describe {type} {name} <br>
kubectl -n {namespace} logs -f {pod name} <br>
kubectl -n {namespace} get events --sort-by='.lastTimestamp' <br>
kubectl -n {namespace} exec -it {pod name} bash <br>
kubectl -n {namespace} scale --replicas=1 deploy {deployment name} <br>
<br>
root@minikube:/# cat etc/hosts <br>
127.0.0.1	localhost <br>
::1	localhost ip6-localhost ip6-loopback <br>
fe00::0	ip6-localnet <br>
ff00::0	ip6-mcastprefix <br>
ff02::1	ip6-allnodes <br>
ff02::2	ip6-allrouters <br>
192.168.49.2	minikube<br>
192.168.65.2	host.minikube.internal <br>
192.168.49.2	control-plane.minikube.internal <br>

#brew install helm <br>
helm create {chart name}  CREATE FOLDER CHART <br>
helm -n {namespace} install {helm name} {folder helm} <br>
helm -n {namespace} uninstall {helm name} <br>
helm -n {namespace} list <br>

#install aws cli <br>
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"  <br>

sudo apt install unzip <br>

sudo unzip awscliv2.zip  <br>

sudo ./aws/install <br>

aws --version <br>

#install helm <br> 
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 <br>
sudo chmod 700 get_helm.sh <br>
sudo ./get_helm.sh <br>
helm version --client <br>
#install kube <br>
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo touch /etc/apt/sources.list.d/kubernetes.list <br>
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list <br>
sudo apt-get update <br>
sudo apt-get install -y kubectl <br>
#install eks <br>
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin <br>

#Docker Jenkins Integration <br>
Add jenkins user to Docker group<br>
sudo usermod -a -G docker jenkins <br>

Restart Jenkins service <br>
sudo service jenkins restart<br>

Reload system daemon files<br>
sudo systemctl daemon-reload<br>

Restart Docker service as well<br>

sudo service docker stop<br>
sudo service docker start<br>



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

 

