Task – Setup basic cloud components in Azure
--------------------------------------------

Basic cloud components are needed first. Things like Virtual Networks and
Resource Groups are required prior to deployment of virtual machines.
You will configure these items in preparation for the WordPress
deployment.

#. Log into the Microsoft Azure Portal – https://portal.azure.com
#. Click the green **+ Create a resource** at the top left corner of the screen
#. Click on **Networking**
#. Click on **Virtual network**

   .. image:: /_static/image57.png
      :scale: 50 %

   Use the information provided in Table 2.1 below to create a virtual network.
   Replace **##** with your assigned student#.

   Table 2.1

   +-----------------------+---------------------------------------+
   | Key                   | Value                                 |
   +=======================+=======================================+
   | Name                  | student##_vnet                        |
   +-----------------------+---------------------------------------+
   | Address space         | 10.10.0.0/16                          |
   +-----------------------+---------------------------------------+
   | Subscription          | <User Unique>                         |
   +-----------------------+---------------------------------------+
   | Resource group        | Create new: student##-rg              |
   +-----------------------+---------------------------------------+
   | Location              | <Closest Azure DC>                    |
   +-----------------------+---------------------------------------+
   | Address Range         | 10.10.0.0/22                          |
   +-----------------------+---------------------------------------+

   .. image:: /_static/image58.png
      :height: 550px

#. Click **Create** then continue after "Deployment succeeded" notification.

Task – Deploy WordPress within Azure
------------------------------------

In this task you will deploy a virtual machine and install the
WordPress application.

#. Click the green **+ Create a resource** sign at the top left corner of the screen
#. Start searching the marketplace by typing 'bitnami wordpress' in the search field and hit **Enter**
#. Select **WordPress Certified by Bitnami**

   .. image:: /_static/image33.png
      :height: 450px

#. Click on **Create** at the bottom of the screen

   Use the information in Table 2.2 to complete the “Basics” configuration
   page during this deployment.

   Table 2.2

   +-----------------------+-------------------------------------------------+
   | Key                   | Value                                           |
   +=======================+=================================================+
   | Resource Group        | Use existing: student##-rg                      |
   +-----------------------+-------------------------------------------------+
   | Virtual machine name  | student##-wp                                    |
   +-----------------------+-------------------------------------------------+
   | Region                | <Closest Azure DC>                              |
   +-----------------------+-------------------------------------------------+
   | Size                  | Change: Basic A1                                |
   +-----------------------+-------------------------------------------------+
   | Authentication type   | Password                                        |
   +-----------------------+-------------------------------------------------+
   | Username              | azureuser                                       |
   +-----------------------+-------------------------------------------------+
   | Password              | ChangeMeNow123                                  |
   +-----------------------+-------------------------------------------------+

   .. image:: /_static/image59.png
      :height: 500px

#. Click **Review + create** at the bottom of the page
#. Supply your email and phone number for validation

   .. image:: /_static/lab-instance-validation.png
      :height: 400px

#. Click **Purchase** or **Create**.  You will receive status "Deployment underway".
   Continue on after receiving "Your deployment is complete".
#. Go to **Resource groups** and click on your resource group
#. Select your WordPress “Public IP address”

   .. image:: /_static/image61.png
      :height: 400px

   .. image:: /_static/image62.png
      :height: 400px

#. Verify that \https://<WordPress-Public-IP> displays the
   Wordpress blog

   - You may have to accept the security warning

   .. image:: /_static/image54.png
      :height: 400px

   .. Note::
      Remember the WordPress public IP address. This will be used in
      subsequent steps.  This can take 10min before page loads.

Task – Deploy a new F5 BIG-IP VE in Azure
-----------------------------------------

In this task you will deploy a virtual machine and install the
BIG-IP.

#. Click the green **+ Create a resource** sign at the top left corner of the screen
#. Search the marketplace by typing 'F5 Better' in the search field and hit **Enter**.
   Take your time to view the different F5 products available.

#. Click **F5 BIG-IP Virtual Edition - BETTER (PAYG, 25Mbps)**

   .. image:: /_static/image9.png
      :height: 400px

   .. NOTE::
      All hourly offerings include a 30 day free trial as well as access to F5 premium support.

#. Click **Create**

   You will now start the deployment process. Use the information provided
   in Table 1.1 below to complete the “Create virtual machine” Basics page.

   Table 1.1

   +-----------------------+-------------------------------------------------+
   | Key                   | Value                                           |
   +=======================+=================================================+
   | Resource Group        | Use existing: student##-rg                      |
   +-----------------------+-------------------------------------------------+
   | Virtual machine name  | student##-f5                                    |
   +-----------------------+-------------------------------------------------+
   | Region                | <Closest Azure DC>                              |
   +-----------------------+-------------------------------------------------+
   | Size                  | Change: Standard DS2_v2                         |
   +-----------------------+-------------------------------------------------+
   | Authentication type   | Password                                        |
   +-----------------------+-------------------------------------------------+
   | Username              | azureuser                                       |
   +-----------------------+-------------------------------------------------+
   | Password              | ChangeMeNow123                                  |
   +-----------------------+-------------------------------------------------+
   | Public inbound ports  | Allow selected ports                            |
   +-----------------------+-------------------------------------------------+
   | Selected inbound ports| HTTPS                                           |
   +-----------------------+-------------------------------------------------+

   Example:

   .. image:: /_static/image11.png
      :height: 400px

   .. image:: /_static/image13.png
      :height: 150px

#. Click **Review + Create**
#. Review the "Summary" page and the purchase you are about to make
#. Supply your email and phone number for validation

   .. image:: /_static/image14.png
      :height: 400px

#. Click **Create**.  You will receive status "Deployment underway".
   Continue on after receiving "Your deployment is complete".

Task – Allow management and HTTPS access to the BIG-IP
------------------------------------------------------

In this task you will permit management access and HTTPS access to the
BIG-IP by modifying the Network Security Group “Inbound” network access
rule set.

#. Select the **student##-f5-nsg** Network security group

   .. image:: /_static/image17.png
      :height: 300px

#. Review the existing ruleset. Notice the default inbound rules and HTTPS
   selected during an earlier step.

   .. image:: /_static/image18.png
      :height: 300px

   Now you will add rules to allow HTTPS on port 8443 for F5 BIG-IP management
   by clicking on “Inbound security rules” (to the left of the screen below).

#. Click **Inbound security rules**

   .. image:: /_static/image19.png
      :height: 200px

#. Click **+ Add**

   Using the information provided in Table 1.4, add a rule to allow F5
   BIG-IP management traffic.

   Table 1.4

   +--------------------+-------------------+
   | Key                | Value             |
   +====================+===================+
   | Source             | Any               |
   +--------------------+-------------------+
   | Source Port        | \*                |
   +--------------------+-------------------+
   | Destination        | Any               |
   +--------------------+-------------------+
   | Destination Port   | 8443              |
   +--------------------+-------------------+
   | Protocol           | Any               |
   +--------------------+-------------------+
   | Action             | Allow             |
   +--------------------+-------------------+
   | Priority           | 100               |
   +--------------------+-------------------+
   | Name               | f5_mgmt_8443      |
   +--------------------+-------------------+

   .. image:: /_static/image21.png
      :height: 400px

#. Click **Add**
#. When complete, verify the end results look as follows:

   .. image:: /_static/image22.png
      :height: 200px

#. Select **Resource Group > student##-rg > student##-f5** then **networking**
   to view public and private address of the F5 BIG-IP virtual machine.

   .. image:: /_static/image20.png
      :height: 300px

#. Connect to the F5 GUI by going to **https://<F5-BIG-IP-public-IP>:8443**
#. Accept the SSL certificate warning
#. Log into the BIG-IP using the credentials configured in the previous steps
   Username: azureuser
   Password: ChangeMeNow123

Task – Allow Internet access to WordPress through the BIG-IP
------------------------------------------------------------

In this task you will configure the BIG-IP with a Virtual Server and
Pool to allow inbound Internet access to the WordPress application. First we
need to identify the private IP address for the WordPress instance. Let's go
back to the Microsoft Azure Portal.

#. Select **Resource Group > student##-rg > student##-wp** then **networking**
   to view public and private address of the F5 BIG-IP virtual machine.

   .. image:: /_static/image47.png
      :scale: 50 %

   .. Note::
      Remember WordPress private IP address. This will be used in
      subsequent steps.

#. Connect to the BIG-IP using \https://<F5-public-IP>:8443
#. From the BIG-IP GUI, go to **Local traffic -> Pools -> Pool List** and
   click on the **+** sign. Configure the pool using the information
   provided in Table 1.8 below leaving all other fields set to defaults.

   Table 1.8

   +-------------------+---------------------------------------+
   | Key               | Value                                 |
   +===================+=======================================+
   | Name              | wordpress_pool                        |
   +-------------------+---------------------------------------+
   | Health Montitor   | HTTPS                                 |
   +-------------------+---------------------------------------+
   | Node Name         | wordpress                             |
   +-------------------+---------------------------------------+
   | Address           | <your WordPress private IP address>   |
   +-------------------+---------------------------------------+
   | Service Port      | 443                                   |
   +-------------------+---------------------------------------+

   .. image:: /_static/image49.png
      :scale: 50 %

#. Click **Finished**. When configured correctly, the pool status will be green.

   .. image:: /_static/image50.png
      :scale: 50 %

   You now need to configure the Virtual server. To do this, you first need to
   find the private IP of your F5 BIG-IP.

#. From the BIG-IP GUI, go to **Network -> Self IPs** and note the IP Address

   .. image:: /_static/image51.png
      :scale: 50 %

#. Create a virtual server by going to
   **Local Traffic -> Virtual Servers -> Virtual Server List** and click
   on the **+** sign. Configure the Virtual Server using the information
   provided in Table 1.9 below leaving all other fields set to defaults.

   Table 1.9

   +------------------------------+-----------------------------------+
   | Key                          | Value                             |
   +==============================+===================================+
   | Name                         | vs_wordpress                      |
   +------------------------------+-----------------------------------+
   | Destination Address          | <Self IP address of the BIG-IP>   |
   +------------------------------+-----------------------------------+
   | Service Port                 | 443                               |
   +------------------------------+-----------------------------------+
   | SSL Profile (Client)         | clientssl                         |
   +------------------------------+-----------------------------------+
   | SSL Profile (Server)         | serverssl                         |
   +------------------------------+-----------------------------------+
   | Source Address Translation   | Auto Map                          |
   +------------------------------+-----------------------------------+
   | Default Pool                 | wordpress_pool                    |
   +------------------------------+-----------------------------------+

   .. image:: /_static/image52.png
      :scale: 50 %

   .. image:: /_static/image53.png
      :scale: 50 %

#. Click **Finish**

   You have now completed the BIG-IP configuration for the WordPress
   application. To verify proper functionality, let's browse the site and
   verify F5 statistics.

#. Open a browser to to \https://<F5-public-VIP-IP> and ensure it
   displays your WordPress blog.

   .. NOTE::
      As part of this task, you will see a certificate warning. You can
      ignore this as in this lab you did not generate the server certificates.
      In real life, you would ensure you have installed valid certificates.

#. Now check the statistics of your virtual server to verify traffic flow,
   by navigating to **Statistics -> Module Statistics -> Local Traffic**
#. Under **Statistics Type**, select **Virtual Servers**

   .. image:: /_static/image55.png
      :scale: 50 %

Task – Disable direct Internet access to WordPress
--------------------------------------------------

You now need to modify the Network security group to remove direct
inbound access to the WordPress application.

#. Select **Resource Group > student##-rg > student##-wp-nsg** then **inbound security rules**

   .. image:: /_static/image43.png
      :scale: 50 %

#. Click on the **…** link at the far right side of the HTTPS inbound
   rule and select **Delete**

   .. image:: /_static/image45.png
      :scale: 50 %

   .. Note::
      You will only allow web access to the WordPress blog via the F5 BIG-IP.

#. Confirm the delete action when prompted by clicking **Yes**
#. Verify that \https://<WordPress-Public-IP> and \http://<WordPress-Public-IP>
   do *NOT* display the WordPress blog

   .. image:: /_static/image46.png
      :scale: 50 %

Task – Lab 1 Teardown
---------------------
Skip this Task if you intend to do the Azure Security Center Lab.

#. Delete resource group **student##-rg** created earlier in this lab.

   - From Azure Portal select **Resource Group**
   - Select **...** on right side of the resource group created earlier
   - Select **delete**.  You will be prompted to enter resource again for confirmation.

#. Enter resource group name when prompted for resource group to be deleted.

   .. image:: /_static/image56.gif
      :scale: 50 %

**This concludes Lab 1**
