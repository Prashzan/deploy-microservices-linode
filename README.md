The link for the project used in this lecture can be found here: https://github.com/techworld-with-nana/microservices-demo

-------------------- Deploy Using microservices.yaml config file ------------------------

#### -- Basically we need to know the image name for each microservice and what env varibales each microservice expects
#### -- We should know on which port each microservice starts
#### -- Microservices is deployed in same namespace

To connect to Kubernetes cluster which is in Linode Server, Spin up kubernetes cluster in Linode 

#### Download the Kubeconfig file from Linode (kubeconfig.yaml)
Before we use the kubeconfig file, we have to set it permission to more strict permissions.. cuz it contains credentials to our kubernetes cluster and securely store this file

#### -- chmod 600 kubeconfig.yaml (only for the user or owner)

#### -- export KUBECONFIG = kubeconfig.yaml

#### -- kubectl get node (to test the cluster)

#### -- kubectl create ns microservices (creating the namespace named microservices to deploy)

#### -- kubectl apply -f microservices.yaml -n microservices (deployment and service component created)

#### -- kubectl get pod -n microservices

#### -- kubectl get svc -n microservices


------------------------ Deploy Using Helm File -------------------------

#### 1. Download the GPG key to verify the package
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null

#### 2. Install apt-transport-https (required for some Ubuntu versions)
sudo apt-get install apt-transport-https --yes

#### 3. Add the Helm repository to your system
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list

#### 4. Update and install Helm
sudo apt-get update
sudo apt-get install helm

#### 5. Add the Bitnami repository
helm repo add bitnami https://charts.bitnami.com/bitnami

#### 6. Update your local cache to see the latest charts
helm repo update


To run the program or install all the charts
make the install.sh script executable 
#### -- chmod u+x install.sh

run the script file
#### -- ./install.sh

To uninstall all the charts or services
#### -- chmod u+x uninstall.sh
#### -- ./uninstall.sh

(for each services or releases) helm command doesn't support installing, updating or uninstalling multiple releases with a single command, there is another way to do nicely, i.e. using Helmfile

Helmfile -- Declarative way of telling helm, that we want this releases to run on my clusters with this configuration (define a desired state like in terraform) and allows us to declare a definition of entire kubernetes cluster in a single yaml file

To install helmfile tool
#### -- curl -LO https://github.com/helmfile/helmfile/releases/download/${VERSION}/helmfile_linux_amd64
#### -- chmod +x helmfile_linux_amd64
#### -- sudo mv helmfile_linux_amd64 /usr/local/bin/helmfile

To update the cluster or sync the cluster with whatever we have declared in helmfile as a desired state
#### -- helmfile sync

to see the list
#### -- helmfile list (installed releases)

Now you can go to LoadBalancer IP to see the application

To uninstall all the releases with one command
#### -- helmfile destroy

Helm Charts are hosted in Git repository (same repo with application code or different repo


-----------  Some Validation commands ---------------

#### To create a helm chart named micoservice templates and values
#### -- helm create microservice

to validate the templates and values are correct, how do we know we are producing valid kubernetes yaml files
#### -- helm template -f values/service-values.yaml charts/microservice (microservice is the chart name)

Helm Rendering Process -- when helm evaluates chart, it will send templates files to helm template engine, the engine replaces all variables or placeholders in the template files with the actual values from values.yaml (default, user provided), helm will then collect the result of rendering of those templates and send those files to kubernetes when we execute helm install command. 

to check the chart for possible issues(to validate syntax)
#### -- helm lint -f values/service-values.yaml charts/microservice

to create a release or to install a chart (deploy a service, lets say a email service)
#### -- helm install -f email-service-values.yaml emailservice charts/microservice

to see the list of microservice running as a chart
#### -- helm ls
#### -- kubectl get pod (to get the pods running)
 
(to plan what's going to happen)
#### -- helm install --dry-run -f values/redis-values.yaml rediscart charts/redis

