# Lab 5- Deploy Multi-Region Fleet Tracking on Azure Linux

## Scenario

Northwind Logistics operates vehicle tracking services across multiple
regional distribution centers.

Each new region requires a fleet tracking server that provides:

- Vehicle tracking

- Fleet health monitoring

- Dispatch operations

Currently, administrators manually configure every server, resulting in:

- Configuration drift

- Inconsistent deployments

- Increased administrative effort

To standardize operations, Northwind Logistics will create a reusable
Azure Linux image and deploy identical fleet-tracking servers across
multiple locations.

## Objectives

After completing this lab, you will be able to:

- Deploy a fleet-tracking application on Azure Linux.

- Create an Azure Compute Gallery.

- Capture and publish Azure Linux images.

- Create image versions.

- Deploy Azure Linux virtual machines from Gallery images.

- Implement a standardized deployment strategy.

## Exercise 1: Deploy a Fleet Tracking Dashboard

### Task 1: Create an Azure Linux Virtual Machine

1.  Sign in to the Azure portal- +++<https://porta.azure.com>+++ and
    sign in with your Azure credentials.

&nbsp;

3.  Select Virtual Machines tile .Select: **Create \> Virtual Machine**

> ![](./media/image1.png)

4.  Configure the VM using the following settings:

- Resource Group:ResourceGroup1

- Virtual Machine Name: +++**fleettrackervm+++**

- Region: Japan East

- Availability options : No infrastructure redundancy required

- Security type : Standard

- Image: see all images-search Azure Linux and select Azure Linux
  4-select

- Authentication Type: Password

- Key pair name : +++**vmKey5+++**

> Select **Review + Create**.

5.  Select **Create**.

![](./media/image2.png)

![](./media/image3.png)

![](./media/image4.png)

![](./media/image5.png)

6.  Review the details and click on **Download private key and create
    resource.**

> ![](./media/image6.png)

7.  Wait for deployment to be completed and then click on **Go to
    resource.**

![](./media/image7.png)

8.  Copy Primary NIC public address to connect VM later in the lab.

![](./media/image8.png)

9.  Open Visual Studio code form Desktop and open Terminal-\>Git Bash

![](./media/image9.png)

10. **Set permissions on the SSH key.**

+++chmod 400 ~/Downloads/vmKey5.pem+++

![](./media/image10.png)

11. Update below command with Azure Linux VM public IP and then run

+++ssh -i ~/Downloads/vmKey5.pem azureuser@\<PUBLIC_IP\>+++

![](./media/image11.png)

12. Run below commands to install required packages on VM.

Update the VM:

+++sudo tdnf update -y+++

\`\`\`

sudo tdnf install -y \\

python3 \\

python3-pip \\

git

\`\`\`

![](./media/image12.png)

![](./media/image13.png)

13. Run below command to create app folder.

+++mkdir fleet-tracker+++

+++cd fleet-tracker+++

![](./media/image14.png)

14. Create the application file +++vi app.py+++ and add the below code.

![](./media/image15.png) Add the below code .Save the file:Esc+:wq
,Press: Enter

\`\`\`

from flask import Flask

app = Flask(\_\_name\_\_)

@app.route("/")

def home():

return """

\<h1\>Northwind Logistics Fleet Dashboard\</h1\>

\<h3\>Active Vehicles\</h3\>

\<ul\>

\<li\>Truck-101 : Bengaluru\</li\>

\<li\>Truck-205 : Chennai\</li\>

\<li\>Truck-307 : Hyderabad\</li\>

\<li\>Truck-410 : Pune\</li\>

\</ul\>

\<p\>Status : Operational\</p\>

"""

app.run(host="0.0.0.0", port=5000)

\`\`\`

![](./media/image16.png)

15. **Run below command to** Install Flask:

+++pip3 install --user flask+++

![](./media/image17.png)

16. Start the Fleet Tracking Application.Run- **python3 app.py**

![](./media/image18.png) 10. Leave the application running.

### Task 7: Configure Network Access

1.  Return to Azure Portal. Navigate to fleettrackervm-\>Networking
    -\>Network Settings.

![](./media/image19.png)

2.  Select **Create port rule -\>Inbound port rule** and configure:

Destination Port Ranges:+++5000+++

Protocol:TCP

Action:Allow

Select:Add

![](./media/image20.png)

![](./media/image21.png)

![](./media/image22.png)

17. Open a browser.Navigate to:

+++http://\<public-ip\>:5000+++

![](./media/image23.png)

## Exercise 2: Create an Azure Compute Gallery

### Task 1: Create a Gallery

1.  Search for +++**Azure Compute Gallery+++ and s**elect it.

![](./media/image24.png)

2.  Select **Create**

![](./media/image25.png)

3.  Configure with below details and click on **Review + Create.**

Gallery Name: +++**fleetGallery+++**

Subscription: Current Subscription

Resource Group: ResourceGroup1

Region: Japan East

![](./media/image26.png)

4.  Once validation passed ,click **Create**.

![](./media/image27.png)

![](./media/image28.png)

## Exercise 3: Create a Golden Fleet Tracking Image

1.  Return to the SSH session. Stop the application: Ctrl + C

![](./media/image29.png)

2.  Navigate to fleettrackervm -\> Overview and select **Stop** and wait
    until the VM status shows:

![](./media/image30.png)

![](./media/image31.png)

3.  Navigate to fleettrackervm . click on Capture and select Image

![](./media/image32.png)

4.  Select Target Azure compute gallery : **fleetGallery**

> ![](./media/image33.png)

5.  Click on **Create** under **Target VM image definition** field

![](./media/image34.png)

6.  Create an Image Definition with below details and click on OK.

Image Definition Name: +**++leet-tracking-image+++**

Publisher: +++**Northwind+++**

Offer: +++**FleetTracking+++**

SKU:+++ v1+++

![](./media/image35.png)

7.  Create image version with Version: **1.0.0** and then click on
    **Review + create.**

![](./media/image36.png)

8.  Once the validation passed, click on **Create**

> ![](./media/image37.png)

9.  Wait for image creation to be completed.

![](./media/image38.png)

![](./media/image39.png)

10. Click on **Go to resource.**

![](./media/image40.png)

## Exercise 4: Deploy a Regional Fleet Tracking Server

Northwind Logistics is opening a new regional dispatch center. Instead
of rebuilding a VM manually, administrators will use the image.

1.  Click on **Crete VM**

![](./media/image41.png)

2.  Configure VM with below details and then click on Review + Create

- Resource Group : **ResoruceGroup1**

- Virtual Machine Name: +++**fleettracker-east+++**

- Region:Japan East

- Image : image you had created earlier

- Key paid name : +++eastkey+++

- Select inbound ports: 80,22

![](./media/image42.png)

![](./media/image43.png)

3.  After the validation passed, click on **Create**.

![](./media/image44.png)

4.  Click on Download private key and create resource.

![](./media/image44.png)

5.  Wait for the deployment to complete. Click on **Go to resource.**

![](./media/image45.png)

![](./media/image46.png)

6.  Copy IP address

> ![](./media/image47.png)

7.  Click on **Network-\> Networking Settings -\> Create port tule- \>
    inbound port rule**.

![](./media/image48.png)

8.  Configure rule with below details and click **Add**

- Destination port range : +++5000+++

- Protocol :TCP

- Name : +++eastfleettrack+++

![](./media/image49.png)

9.  Switch back to VS code and run below commands to connect to the
    above vm. **Set permissions on the SSH key.** For macOS or Linux:

+++chmod 400 ~/Downloads/eastKey.pem+++

![](./media/image50.png)

10. **Connect to the VM.**Replace \<PUBLIC_IP\> with the public IP
    address returned during VM creation. If prompted to trust the host,
    type yes and press Enter.

+++ssh -i ~/Downloads/eastkey.pem azureuser@\<PUBLIC_IP\>+++

![](./media/image51.png)

11. Run ls to verify application files are available.

![](./media/image52.png)

12. Navigate to fleet-tracker and check if app.py file is available

+++cd fleet-tracker+++

+++ ls+++

![](./media/image53.png)

13. Run below command to start the application.

+++python3 app.py+++

![](./media/image54.png)

14. Browse to: +++http://\<regional-vm-ip\>:5000+++ .should see
    Northwind Logistics Fleet Dashboard

![](./media/image55.png)

## Exercise 5: Deploy a Disaster Recovery Fleet Tracking Server (Optional)

Northwind Logistics requires a backup server for business continuity.

### Task 1: Deploy Another VM

1.  Repeat the previous deployment using: fleet-tracking-image

- Version 1.0.0

- Configure:

- Virtual Machine Name:+++**fleettracker-dr+++**

- Deploy the VM.

  1.  **Connect to the DR Server**

&nbsp;

- Connect via SSH.

  - Verify Azure Linux.Run cat /etc/os-release

  - Start Fleet Dashboard .Run

  - cd fleet-tracker

  - python3 app.py

  1.  Open +++http://\<dr-vm-ip\>:5000+++

  2.  Compare the following:

&nbsp;

- fleettrackervm

- fleettracker-east

- fleettracker-dr

  1.  Verify all servers display:Northwind Logistics Fleet Dashboard

18. Verify operating system consistency.Run on each VM:

+++cat /etc/os-release+++

## Summary

In this lab, you:

- Deployed a fleet tracking application on Azure Linux.

- Created an Azure Compute Gallery.

- Created an image definition and image version.

- Captured a reusable Azure Linux image.

- Deployed regional and disaster recovery fleet-tracking servers from
  the same image.

- Validated standardized deployments across multiple servers.

This demonstrates how Azure Linux image lifecycle management helps
logistics organizations deploy consistent, repeatable infrastructure
across multiple operational environments.
