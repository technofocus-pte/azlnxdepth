# Lab 3 - Build Secure Payment Processing with Azure Container Linux

## Scenario

Northwind Bank is modernizing its payment processing platform.

The development team wants to:

- Build a payment API.

- Package the API as a container image.

- Store the image in Azure Container Registry.

- Deploy the application onto an Azure Container Linux (ACL) based AKS
  cluster.

- Enable monitoring and validate secure operations.

## Objectives

After completing this lab, you will be able to:

- Create and configure an Azure Linux development environment.

- Develop and test a secure payment processing API using Python and
  Flask.

- Containerize an application using Docker.

- Build and manage container images.

- Create and configure Azure Container Registry (ACR).

- Push container images to Azure Container Registry.

- Deploy and validate an Azure Container Linux (ACL) based AKS cluster.

- Deploy containerized workloads to Azure Kubernetes Service (AKS).

- Publish applications using Kubernetes Services.

- Enable monitoring and validate secure operations in an Azure Container
  Linux environment.

## Exercise 1: Create an Azure Linux Development VM

### Task 1: Create Azure Linux VM

1.  Opena browser and enter +++<https://portal.azure.com>+++ and sign in
    with your Azure credentials.

2.  Search for +++Virtual Machines+++ and select it.

![](./media/image1.png)

3.  Select **Create → Azure Virtual Machine**

> ![](./media/image2.png)

4.  Enter below details and create VM.

- Resource Group: ResourceGroup1

- Virtual Machine Name: +++**bankdevvm+++**

- Region: **Japan East**

- Availability options : **No infrastructure redundancy required.**

- Security Type : Standard

- Image: **See all image-\> Search Azure Linux and select Azure Linux
  4.- create**

- Key Pair name : +++**myKey+++**

- **Review + Create**

![](./media/image3.png)

![](./media/image4.png)

![](./media/image5.png)

![](./media/image6.png)

![](./media/image7.png)

5.  After the validation passed, click on **Create**.

![](./media/image8.png)

6.  Click on **Download private key and create resource.**

![](./media/image9.png)

7.  Wait for the deployment to complete and then click on **Go to
    resource.**

![](./media/image10.png)

![](./media/image11.png)

8.  Copy Primary **NIC public IP address** and save it in notepad to
    connect to VM.

> ![](./media/image12.png)

### Task 2: Connect to VM

1.  Open **VS code -\> Terminal**

![](./media/image13.png)

2.  Open **Git Bash** terminal

> ![](./media/image14.png)

3.  **Set permissions on the SSH key.** For macOS or Linux:

+++chmod 400 ~/Downloads/myKey.pem+++

![](./media/image15.png)

4.  **Connect to the VM.**Replace \<PUBLIC_IP\> with the public IP
    address returned during VM creation. If prompted to trust the host,
    type yes and press Enter.

+++ssh -i ~/Downloads/myKey.pem azureuser@\<PUBLIC_IP\>+++

![](./media/image16.png)

## Task 3: Install Development Tools

1.  Run below command to update packages.

+++sudo tdnf update -y Install Python and pip:+++

+++sudo tdnf install -y python3 python3-pip git+++

![](./media/image17.png)

![](./media/image18.png)

2.  Run below command to verify python versions in Azure Linux VM.

+++python3 --version+++

+++pip3 --version+++

![](./media/image19.png)

## Exercise 2: Create Secure Payment API

### Task 1: Create Application

1.  Open new instance of GitBash and run below command to create folder.

**+++mkdir payment-api+++**

**+++cd payment-api+++**

+++pwd+++

![](./media/image20.png)

2.  Click on File- \> Open Folder and open the **payment-api folder**

![](./media/image21.png)

3.  Create app.py . copy the below code and save the file

> \`\`\`
>
> from flask import Flask, jsonify
>
> app = Flask(\_\_name\_\_)
>
> @app.route("/")
>
> def home():
>
> return "Northwind Bank Secure Payment API"
>
> @app.route("/payment")
>
> def payment():
>
> return jsonify({
>
> "transactionId": "TXN10001",
>
> "cardNumber": "\*\*\*\*1234",
>
> "merchant": "Northwind Retail",
>
> "amount": "5000",
>
> "currency": "INR",
>
> "status": "Approved"
>
> })
>
> app.run(host="0.0.0.0", port=5000)
>
> \`\`\`

![](./media/image22.png)

![](./media/image23.png)

## Task 3: Install Flask and run the application

1.  Open New Terminal and select Git Bash

![](./media/image24.png)

2.  Run below command

+++pip3 install --user flask+++

+++python -m pip show flask+++

+++python3 app.py+++

![](./media/image25.png)

![](./media/image26.png)

3.  Click on the app link

![](./media/image27.png)

4.  App open in browser.

![](./media/image28.png)

![](./media/image29.png)

## Task 5: Validate Payment Endpoint

1.  Open new instance second SSH session and run blow curl command.

+++curl <http://localhost:5000/payment>+++

![](./media/image30.png)

2.  Switch back first instance stop application: Ctrl+C

![](./media/image31.png)

## Exercise 3: Containerize the Payment API

Build this portion on the machine where Docker is available.

### Task 1: Create Dockerfile ,Requirements File

1.  Run below command to create requirements file

> +++echo "flask" \> requirements.txt+++

+++cat requirements.txt+++

![](./media/image32.png)

2.  Create dockerfile +++ vi Dockerfile+++ and add the below code

\`\`\`

FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY app.py .

EXPOSE 5000

CMD \["python","app.py"\]

\`\`\`

![](./media/image33.png)

3.  Press Esc, +++:wq+++ to save the file.

![](./media/image34.png)![](./media/image35.png)  
Task 4: Build Container Image

1.  Double click on the Docker icon from desktop.

![](./media/image36.png)

2.  Click on **Accept**.

![](./media/image37.png)

3.  Click on skip.

4.  Click on **Skip** on Welcome to Docker page.

![](./media/image38.png)

5.  Make sure the Docker is running.

![](./media/image39.png)

6.  Switch back to Vs code. Run below command to build container image

> +++docker build -t payment-api:v1 .+++

![](./media/image40.png)

![](./media/image41.png)

### Task 5: Verify Image

1.  Run below command to check

+++docker images+++

![](./media/image42.png)

### Task 6: Run Container

1.  Run below command to run the container.

+++docker run -d -p 5000:5000 payment-api:v1+++

![](./media/image43.png)

### Task 7: Validate Container

1.  Run below command to check the app.

+++curl <http://localhost:5000/payment>+++

![](./media/image44.png)

## Exercise 4: Create Azure Container Registry

### Task 1: Create Container Registry

1.  Switch back Azure portal and open Cloud shell.

![](./media/image45.png)

2.  Select **Bash**.

![](./media/image46.png)

3.  Select subscription and click on **Apply**.

![](./media/image47.png)

4.  Run below command set environment variable

\`\`\`

export RESOURCE_GROUP=ResourceGroup1

export ACR_NAME=bankacr12345

export ACR_NAME="bankacr12345"

\`\`\`

![](./media/image48.png)

5.  Run below command to create container registry.

+++az acr create --resource-group $RESOURCE_GROUP --name $ACR_NAME --sku
Basic+++

![](./media/image49.png)

6.  Run below command to enable administrator.

+++az acr update --name $ACR_NAME --admin-enabled true+++

![](./media/image50.png)

7.  Run below command to retrieve registry credentials

> +++az acr show --name $ACR_NAME --query loginServer -o tsv+++

![](./media/image51.png)

8.  Run below command to get acr user name and password

> +++az acr credential show --name $ACR_NAME --query username -o tsv+++

+++az acr credential show --name $ACR_NAME --query passwords\[0\].value
-o tsv+++

![](./media/image52.png)

**\> \[!note\]**Do not run az acr login in Azure Cloud Shell. Cloud
Shell doesn't support the Docker daemon. Use Cloud Shell only to obtain
registry credentials and perform the Docker login from the Skillable VM
where Docker is installed.

9.  Switch back to Visual Studio code .Replace \<\<ACR_NAME\>\> with the
    acr name retrieved from above command and run it. Enter username and
    password when

+++az acr login --name \<\<ACR_NAME\>\>+++

![](./media/image53.png)

![](./media/image54.png)

## Exercise 5: Push Application Image into ACR

### Task 1: Retrieve Login Server

1.  Run below command to tage the image

> +++docker tag payment-api:v1 bankacr12345.azurecr.io/payment-api:v1+++

![](./media/image55.png)

2.  **Run below command to push the image**

+++docker push bankacr12345.azurecr.io/payment-api:v1+++

![](./media/image56.png)

![](./media/image57.png)

## Exercise 6: Create Azure Container Linux AKS Cluster

1.  Run below command to create ACL cluster.

\`\`\`

export CLUSTER_NAME=bankaclcluster

export RESOURCE_GROUP=ResourceGroup1

\`\`\`

+++az login+++

+++az aks create --resource-group $RESOURCE_GROUP --name bankaclcluster
--os-sku AzureLinux --node-count 3 --generate-ssh-keys+++

![](./media/image58.png)

![](./media/image59.png)

![](./media/image60.png)

![](./media/image61.png)

![](./media/image62.png)

![](./media/image63.png)

![](./media/image64.png)

## Exercise 7: Connect and Validate Azure Linux Nodes

### Task 1 :Validate ACL nodes

1.  Run below command to get acl credentials

> +++az aks get-credentials --resource-group $RESOURCE_GROUP --name
> bankaclcluster+++

![](./media/image65.png)

2.  Run below command to get nodes. Review node information.

+++kubectl get nodes -o wide+++

![](./media/image66.png)

7.  Run below command to validate node pool OS

> +++az aks nodepool list --resource-group $RESOURCE_GROUP
> --cluster-name bankaclcluster --query
> "\[\].{Pool:name,OS:osSku,Image:nodeImageVersion}" -o table+++

![](./media/image67.png)

## Exercise 8: Add Azure Container Linux Node Pool

### Task 1 : Add node pool

1.  **Run below command to add nodepool**

> +++az aks nodepool add --resource-group $RESOURCE_GROUP --cluster-name
> bankaclcluster --name securepool --node-count 3 --os-sku AzureLinux+++

![](./media/image68.png)

## **Exercise 9: Deploy Payment Application**

### Task 1: Attach Registry

1.  Run below command to get acr name

+++az acr repository list --name $ACR_NAME --output table+++

![](./media/image69.png)

2.  Run below command attach acr (Require owner role)

+++$ az aks update --resource-group $RESOURCE_GROUP --name $CLUSTER_NAME
-attach-acr $ACR_NAME+++

![](./media/image70.png)

### Task 2: Create Deployment Manifest

1.  Create a file +++**payment-deployment.yaml+++** and add the below
    code. Update image name as appropriate.

\`\`\`

apiVersion: apps/v1

kind: Deployment

metadata:

name: payment-api

spec:

replicas: 3

selector:

matchLabels:

app: payment-api

template:

metadata:

labels:

app: payment-api

spec:

containers:

\- name: payment-api

image: bankacr12345.azurecr.io/payment-api:v1

ports:

\- containerPort: 5000

\`\`\`

![](./media/image71.png)

2.  Run below command to deploy.

+++kubectl apply -f payment-deployment.yaml+++

3.  Run below command to verify pods status

+++kubectl get pods+++

## Exercise 10: Publish and Monitor the Secure Payment Platform

1.  Run the command to expose the service

+++kubectl expose deployment payment-api --port=80 --target-port=5000
--type=LoadBalancer+++

2.  Run below command t retrieve service.Wait until EXTERNAL_IP appears

+++kubectl get svc+++

3.  Update the url and run it in browser -http://\<external-ip\>/payment

4.  **Run below command to e**nable monitoring:

+++az aks enable-addons --addons monitoring --resource-group
$RESOURCE_GROUP --name $CLUSTER_NAME+++

5.  Run below command to verify monitoring agent:

+++kubectl get ds ama-logs -n kube-system+++

6.  Verify monitoring deployment:

+++kubectl get deployment ama-logs-rs -n kube-system+++

## Summary

In this lab, you modernized a banking payment processing application
using Azure Linux and Azure Container Linux. You started by creating an
Azure Linux development virtual machine and installed the tools required
to build and test a Python-based payment API. You containerized the
application using Docker, built a container image, and stored it in
Azure Container Registry. You then deployed an Azure Container
Linux-based AKS cluster, validated the cluster nodes, and added a
dedicated node pool to support secure containerized workloads. Finally,
you deployed the payment application to Kubernetes, exposed it through a
Load Balancer service, and enabled monitoring to validate operational
health. By completing this lab, you gained hands-on experience with
application modernization, containerization, Azure Container Registry,
Azure Container Linux, Kubernetes deployment, and secure cloud-native
application operations in a banking scenario.
