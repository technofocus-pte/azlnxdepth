# Lab 9 - Create and Publish a Governed Azure Linux Image

## Scenario

Northwind Manufacturing manages factory-control systems running on Azure
Linux.

Before a Linux image is approved for factory deployment, administrators
must:

- Apply system updates.

- Validate repositories.

- Review logs.

- Verify storage and filesystem health.

- Publish a governed image.

## Objectives

After completing this lab, you will be able to:

- Manage packages with DNF5.

- Review Azure Linux repositories.

- Monitor storage and filesystem health.

- Review logs using journalctl.

- Configure log retention.

- Publish a governed Azure Linux image.

## Exercise 1: Manage Packages Using DNF5

1.  Open a browser and go to +++https:\\portal.azure.com+++ and sign in
    with your Azure credentials. Select virtual machines tile on the
    home page.

> ![](./media/image1.png)

2.  Click on **Create- \> Virtual machine**

![](./media/image2.png)

3.  Configure Azure Linux VM with below details

- Resource Group : ResourceGroup1

- VM Name: +++**manufacturingvm+++**

- Region:Japan East

- Availability options : No infrastructure redundancy required

- Security type : Standard

- Image : See all images-\> Search Azure linux -\> Select Azure linux
  4.0. Create

- Key pair name : +++**manufacturingvm_key+++**

- **Review+ Create.**

![](./media/image3.png)

![](./media/image4.png)

![](./media/image5.png)

2\. Once the validation is passed click on **Create**

![](./media/image6.png)

4.  Click on **Download private key and create resource.**

![](./media/image7.png)

5.  Wait for the deployment to complete , click on **Go to resource.**

![](./media/image8.png)

6.  Copy the public IP address.

![](./media/image9.png)

7.  Open Visual Studio code -\> Terminal -\> New Terminal -\> Git Bash

> ![](./media/image10.png)

8.  **Set permissions on the SSH key.** For macOS or Linux:

+++chmod 400 ~/Downloads/**manufacturingvm_key**.pem+++

> ![](./media/image11.png)

9.  **Connect to the VM.**Replace \<PUBLIC_IP\> with the public IP
    address returned during VM creation. If prompted to trust the host,
    type yes and press Enter.

+++ssh -i ~/Downloads/**manufacturingvm_key** azureuser@\<PUBLIC_IP\>+++

![](./media/image12.png)

10. Run below command to install updates.

+++sudo dnf update -y+++

![](./media/image13.png)

11. Run below command to review repositories.

+++dnf repolist+++

![](./media/image14.png)

12. Review package information by running below command and oberserve
    version, vendor and repository.

+++dnf info openssl+++

![](./media/image15.png)

## Exercise 3: Review Storage Health

1.  Run command to display storage usage.

> +++df -h+++

![](./media/image16.png)

2.  Run below command to display filesystem type.

+++df -T /+++

![](./media/image17.png)

3.  Run below commands and review block devices and file system
    identifiers

> +++lsblk+++
>
> +++blkid+++

![](./media/image18.png)

![](./media/image19.png)

## Exercise 4: Review Networking Configuration

1.  Run below command to review ip config and routes

+++ip addr+++

+++ip route+++

![](./media/image20.png)

![](./media/image21.png)

2.  Review network service and check interface status by running below
    commands.

+++sudo systemctl status systemd-networkd+++

+++networkctl list+++

![](./media/image22.png)![](./media/image23.png)

## Exercise 5: Review Logging and Monitoring

1.  Review system logs and SSH service logs.

+++journalctl+++

+++journalctl -u sshd+++

![](./media/image24.png)

![](./media/image25.png)

3.  Review real-time logs

+++journalctl -f+++

![](./media/image26.png)

4.  Review system messages.Press Ctrl + C after review to stop the log
    messages.

+++sudo tail -f /var/log/messages+++

![](./media/image27.png)

## Exercise 6: Configure Log Retention Policy

1.  Run below command to create sample log.

+++sudo touch /var/log/factory.log+++

![](./media/image28.png)

2.  Create logrotate policy and add below code to it.

+++sudo vi /etc/logrotate.d/factoryapp+++

\`\`\`

/var/log/factory.log {

weekly

rotate 4

compress

missingok

notifempty

}

\`\`\`

![](./media/image29.png)

3.  Save the file – press Esc and enter :wq and press Enter

![](./media/image30.png)

![](./media/image31.png)

4.  Validate configuration by running below command review the output.

+++sudo logrotate -d /etc/logrotate.d/factoryapp+++

![](./media/image32.png)

## Exercise 7: Publish Governed Azure Linux Image

1.  Return to Azure Portal.Search +++Azure Compute Gallery+++ and select
    it

![](./media/image33.png)

2.  Click on Create

![](./media/image34.png)

3.  Create the gallery with below details and then click on **Review +
    create.**

- Resource Group : ResourceGroup1

- Gallery Name- +++**manufacturingGallery+++**

- Region : resource group region

![](./media/image35.png)

4.  Once the validation passed, click on Create.

![](./media/image36.png)

5.  Wati for the deployment to complete and then click on **Go to
    resource.**

![](./media/image37.png)

![](./media/image38.png)

![](./media/image39.png)

5.  Open **manufacturingvm and** Stop it. Wait until Stopped
    (Deallocated)

![](./media/image40.png)

![](./media/image41.png) ![](./media/image42.png)

6.  Select **Capture- \>Image**

![](./media/image43.png)

7.  Create Image Definition.

- Target Azure compute gallery : **manufacturingGallery**

- Target VM image definition : Create new

- Image Name: +++**ManufacturingSecureImage+++**

- Publisher:+++Northwind+++

- SKU -+++v1+++

- Version: +++1.0.0+++

- Select:Review + Create

![](./media/image44.png)

![](./media/image45.png)

![](./media/image46.png)

![](./media/image47.png)

![](./media/image48.png)

8.  Once the validation passed, click on Create.

![](./media/image49.png)

![](./media/image50.png)

## Summary

In this lab, you performed the activities required to prepare and govern
an Azure Linux image for manufacturing environments. You provisioned an
Azure Linux virtual machine and used **DNF5** to update the operating
system, review package repositories, and inspect installed packages. You
then examined system administration components by validating storage
utilization, filesystem types, block devices, networking configuration,
and network services. To support operational monitoring, you reviewed
system logs using **journalctl**, monitored SSH and system events, and
configured a **log rotation policy** to enforce log retention and
prevent uncontrolled log growth. Finally, you implemented image
governance by creating an **Azure Compute Gallery**, capturing the
configured Azure Linux virtual machine, creating an image definition and
version, and publishing a standardized image named
**ManufacturingSecureImage**. By completing this lab, you learned how to
maintain Azure Linux systems, manage packages and repositories, monitor
system health, implement log retention, and create reusable governed
images that can be deployed consistently across manufacturing
environments.
