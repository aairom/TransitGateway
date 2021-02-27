# Transit Gateway Implementation

#with Classic and VPC Instances





---

March 2021

by Philippe THOMAS



---

The purpose of this document is to implement step-by-step a VPC, Subnet, Virtual Servers and Classic Virtual Servers with communication between them thru a **Transit Gateway**.

![image-20210227231625676](images/image-20210227231625676-4464185.png)



# Step #1: Login to IBM cloud

Login to IBM Cloud account with the necessary permissions. 



![image-20210226141609061](images/image-20210226141609061-4345369.png)

---

In the Account Settings verify that **VRF is Enabled**. If not, open a ticket to ask the support to enable VRF. 



# Step #2: Create 2 VSIs on Classic Infra

First we are going to create a Centos VSI with the name of **niceclassiccentos** 

Go to **Catalog**

Search for **Virtual Server for Classic** and Click onn the tile

![image-20210226142150877](images/image-20210226142150877-4345710.png)

Fill in the form:

- type : Public
- hostname : niceclassiccentos
- Location : Europe - FRA02
- Profile : B1.2x4
- Image : Centos 8.x
- Network Interface : Private Only



This look like this example below:

![image-20210226142745739](images/image-20210226142745739-4346065.png)

![image-20210226142810177](images/image-20210226142810177-4346090.png)

Don't define any security groups, specific VLAN or SSH key : you'll will do it later on. 

Click **Create**

![image-20210226142926944](images/image-20210226142926944-4346166.png)

The process of creation is starting. Wait one or 2 minutes.

---

Secondly, we are going to create a **Windows** VSI with the name of **niceclassicwin** 

Go to **Catalog**

Search for **Virtual Server for Classic** and Click onn the tile

![image-20210226142150877](images/image-20210226142150877-4345710.png)

Fill in the form:

- type : Public
- hostname : niceclassicwin
- Location : Europe - FRA02
- Profile : M1.1x8
- Image : Microsoft Windows Server 2016
- Network Interface : **Private** Only



![image-20210226143637422](images/image-20210226143637422-4346597.png)

![image-20210226143745217](images/image-20210226143745217-4346665.png)

Then check the box on the right pane and click Create :

![image-20210226143846470](images/image-20210226143846470-4346726.png)

It will take also a few minutes to create this new VSI.

![image-20210226144204510](images/image-20210226144204510-4346924.png)

Take a note of the different Private IPs





# Step #3: Accessing the classic VSIs

From the list of Devices, click on the Centos VSI : 

![image-20210226144431630](images/image-20210226144431630-4347071.png)

You should get the overview and everything should be green :

![image-20210226144600798](images/image-20210226144600798-4347160.png)

Then click on **Passwords** on the left pane:

![image-20210226144724837](images/image-20210226144724837-4347244.png)

---



Visualise the password and take notes:

**Hostname** : niceclassiccentos

**Private IP** : 10.85.198.16 

**username** : root

**password** : m66zKEx5TSCe   (for example)

Do the same thing for the other VSI (niceclassicwin):

Visualise the password and take notes: you may have to **wait** more minutes to see the Passwords. You should see a Start Date for all VSIs. 

![image-20210226151114338](images/image-20210226151114338-4348674.png)



---



**Hostname** : niceclassicwin

**Private IP** : 10.85.198.25

**username** : Administrator

**password** :v3rsgRNYGKEixVNqDf03   (for example)

---

Now, you are going to get access to the VSIs.

To get access to the classic VSI on the **private network interface**, you need to install MOTION PRO application on your laptop.

Follow this link to install the **Motion Pro client**. 

https://cloud.ibm.com/docs/iaas-vpn?topic=iaas-vpn-standalone-vpn-clients

After you installed the client, you need to add a tunnel.

Because, we created all the VSIs on FRA02, then you need to create a tunnel to the FRA02 zone in IBM Cloud.

Right click and pick **Add**:

![image-20210226150348339](images/image-20210226150348339-4348228.png)

Then fill in the form with your account name and VPN password (that you find in the **Account Settings**):

![image-20210226150253164](images/image-20210226150253164-4348173.png)



Important: press **enter** to create the tunnel.

Here is what you should get :

![image-20210226150021167](images/image-20210226150021167-4348021.png)

 Then open a **terminal** and **ping** the connection with IPs for the VSIs frm your laptop.

![image-20210226151323980](images/image-20210226151323980-4348804.png)





# Step #4: Define VPC and Subnets

From the Catalog, search Virtual Private Cloud and click on the Tile:

![image-20210226151617784](images/image-20210226151617784-4348977.png)

Specify the following definitions:

- name : nicevpc

- Resource Group : admin-resources (your group of resources - this should be consistent for the next definitions)

- subnet name : nicesub 

- Location : Frankfurt 2



  **Leave all the other values as default.** 



Here is the screenshot:

![image-20210226152207896](images/image-20210226152207896-4349327.png)

![image-20210226152231075](images/image-20210226152231075-4349351.png)



Click on **Create**:

![image-20210226152334655](images/image-20210226152334655-4349414.png)

![image-20210226152510955](images/image-20210226152510955-4349510.png)



VPC and Subnets are successfully created.



# Step #5: Define VSIs in the VPC

Click the hamburger menu, click on the VPC Infrastructure then click on Virtual Server Instances :

![image-20210226152725631](images/image-20210226152725631-4349645.png)

On the top of screen, select **Frankfurt**

And click **Create**:

![image-20210226152904134](images/image-20210226152904134-4349744.png)

We are going to create 2 VSIs in the VPC : a Centos one and a Windows 16 one. 

Start with the Centos

- name: **nicevpccentos**
- Resource Group: admin-resources (or your group that is consistent with VPC)
- Location : Frankfurt
- Type : Public
- OS : Centos 8
- Profile : bx2-2x8
- pick a ssh key or create a new one
- VPC : nicevpc





![image-20210226153158241](images/image-20210226153158241-4349918.png)

![image-20210226153411835](images/image-20210226153411835-4350051.png)

![image-20210226153443182](images/image-20210226153443182-4350083.png)

Then click **Create**

![image-20210226153536833](images/image-20210226153536833-4350136.png)

Then the VSI should be running after a while:

![image-20210227135020102](images/image-20210227135020102-4430220.png)

Click on **Create** again.

We are going to create the second  VSI in the VPC : a Windows 16. 

Here are the options for this VSI:

- name: **nicevpcwin**
- Resource Group: admin-resources (or your group that is consistent with VPC)
- Location : Frankfurt
- Type : Public
- OS : Centos 8
- Profile : bx2-2x8
- pick a ssh key or create a new one
- VPC : nicevpc

And some screenshots:

![image-20210227135350948](images/image-20210227135350948-4430430.png)

![image-20210227135447902](images/image-20210227135447902-4430487.png)

![image-20210227135555224](images/image-20210227135555224-4430555.png)

![image-20210227135628341](images/image-20210227135628341-4430588.png)

Then click on **Create** button:

![image-20210227135801345](images/image-20210227135801345-4430681.png)

Wait until the VSI has started. 

![image-20210227140426455](images/image-20210227140426455-4431066.png)

Take some notes about these VSIs:

**Hostname** : nicevpccentos

**Private IP** : 10.243.64.4 

**username** : root

**password** : m66zKEx5TSCe   (for example)

and 

**Hostname** : nicevpcwin

**Private IP** : 10.243.64.5

**username** : Administrator

**password** : m66zKEx5TSCe   (for example)





# Step #6: Define the Transit Gateway

To establish communications between  the 2 classic VSIs and 2 VPC VSIs, we must install a Transit Gateway (TG) : Go to the Catalog and search Transit Gateway. Click on the Tile:`

![image-20210227140940592](images/image-20210227140940592-4431380.png)



Then define you Transit Gateway with the following parameters:

- **TG name**: nicetg
- **Resource Group**: same resource group as VPC
- **Type Location**:  Local Routing
- **Location**: Frankfurt
- **Connection 1**: Classic Infrastructure 
- **Connection 1 name**: toClassic
- **Connection 2** : VPC - Frankfurt
- **Connection 2 VPC** :  nicevpc
- **Connection 2 VPC name** :  nicevpc



Screenshots:



![image-20210227141330047](images/image-20210227141330047-4431610.png)

![image-20210227141505669](images/image-20210227141505669-4431705.png)

On the right pane, click Create button:

![image-20210227141851926](images/image-20210227141851926-4431931.png)

After a few seconds, the TG and connections should active and attached:

![image-20210227142506970](images/image-20210227142506970-4432306.png)





# Step #7: Testing Communications

### test1:  From Classic Centos VSI to VPC VSIs

SSH into the Centos Classic VSI:

For example:

```bash
ssh root@10.85.198.16
```

Results:

``` bash
# ssh root@10.85.198.16
The authenticity of host '10.85.198.16 (10.85.198.16)' can't be established.
ECDSA key fingerprint is SHA256:8gnsr4HYE1eqB9wjwEwH1jceSyci9iRF8+/rmGUIkec.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.85.198.16' (ECDSA) to the list of known hosts.
root@10.85.198.16's password: 
[root@niceclassiccentos ~]# 
[root@niceclassiccentos ~]# 

```

Then ping the VPC VSIs:

**niceclassiccentos -> nicevpccentos**

```bash
ping 10.243.64.4 
```

Results:

```bash
# ping 10.243.64.4 
PING 10.243.64.4 (10.243.64.4) 56(84) bytes of data.
64 bytes from 10.243.64.4: icmp_seq=1 ttl=55 time=1.37 ms
64 bytes from 10.243.64.4: icmp_seq=2 ttl=55 time=1.12 ms
64 bytes from 10.243.64.4: icmp_seq=3 ttl=55 time=1.15 ms
64 bytes from 10.243.64.4: icmp_seq=4 ttl=55 time=11.1 ms
```

 **niceclassiccentos -> nicevpcwin**

```bash
ping 10.243.64.5
```

Results:

```bash
# ping 10.243.64.5
PING 10.243.64.5 (10.243.64.5) 56(84) bytes of data.
64 bytes from 10.243.64.5: icmp_seq=1 ttl=55 time=1.66 ms
64 bytes from 10.243.64.5: icmp_seq=2 ttl=55 time=1.34 ms
64 bytes from 10.243.64.5: icmp_seq=3 ttl=55 time=1.32 ms
64 bytes from 10.243.64.5: icmp_seq=4 ttl=55 time=1.36 ms
64 bytes from 10.243.64.5: icmp_seq=5 ttl=55 time=1.29 ms
```



###test2:  From Classic Windows VSI to VPC VSIs

Now we connect to  the Classic Windows VSI

Start RDP on your laptop:

![image-20210227144059431](images/image-20210227144059431-4433259.png)

Click on the **+** in the bar and the **Add a PC**

Then fill  the form with **IP address** of the classic Windows VSI and give a name like **MyClassicWin**

![image-20210227144543593](images/image-20210227144543593-4433543.png)

Click **Add**

![image-20210227144804749](images/image-20210227144804749-4433684.png)

Click on  the **MyClassicWin**

You will be asked with your credentials: Administrator and password.

![image-20210227145021972](images/image-20210227145021972-4433822.png)

Clcik **Continue**

A popup will ask to continue: Clcik **Continue** again.

You should be  login to  the windows  machine automatically. 

![image-20210227145358886](images/image-20210227145358886-4434038.png)



Open a  Terminal window:

![image-20210227145442136](images/image-20210227145442136-4434082.png)



Then ping the VPC VSIs:

**niceclassicwin -> nicevpccentos**

```bash
ping 10.243.64.4 
```

Results:

```bash
C:\Users\Administrator>ping 10.243.64.4

Pinging 10.243.64.4 with 32 bytes of data:
Reply from 10.243.64.4: bytes=32 time=1ms TTL=55
Reply from 10.243.64.4: bytes=32 time=1ms TTL=55
Reply from 10.243.64.4: bytes=32 time=1ms TTL=55
Reply from 10.243.64.4: bytes=32 time=1ms TTL=55

Ping statistics for 10.243.64.4:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 1ms, Maximum = 1ms, Average = 1ms
```

 **niceclassicwin -> nicevpcwin**

```bash
ping 10.243.64.5
```

Results:

```bash
ping 10.243.64.5

Pinging 10.243.64.5 with 32 bytes of data:
Reply from 10.243.64.5: bytes=32 time=1ms TTL=55
Reply from 10.243.64.5: bytes=32 time=1ms TTL=55
Reply from 10.243.64.5: bytes=32 time=1ms TTL=55
Reply from 10.243.64.5: bytes=32 time=1ms TTL=55

Ping statistics for 10.243.64.5:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 1ms, Maximum = 1ms, Average = 1ms
```



### test3:  From VPC Centos VSI to Classic VSIs

Now we connect to  the VPC Centos VSI. 

We need to  add  a floating IP to the VPC Centos VSI. 

To do it, go to  the description of the VSI in the VPC section:

![image-20210227152055497](images/image-20210227152055497-4435655.png)



At the bottom of the  section, find the network interfaces :

![image-20210227152243051](images/image-20210227152243051-4435763.png)

To add  a **floating IP**, click on the **pen** on the right side of the line and change the button to **Reserve  a new floating IP** like below:

![image-20210227152439493](images/image-20210227152439493-4435879.png)

Then **Save** your change.

![image-20210227152653558](images/image-20210227152653558-4436013.png)

![image-20210227152616439](images/image-20210227152616439-4435976.png)

Take a note of the floating IP. 

SSH into the Centos VPC VSI with the floating IP

For example (you  have  to specify the  SSH key that you define  during the creation)

```bash
ssh  -i test777 root@161.156.160.236
```

Results:

```bash
# ssh  -i test777 root@161.156.160.236
The authenticity of host '161.156.160.236 (161.156.160.236)' can't be established.
ECDSA key fingerprint is SHA256:YGP/le1mPBUFx+gXVoVmHRpFPmWl5Z4wDcR2tU7dAHU.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '161.156.160.236' (ECDSA) to the list of known hosts.
Last login: Fri Nov  6 11:46:27 2020
[root@nicevpccentos ~]# 
[root@nicevpccentos ~]# 
```

Then ping the Classic VSIs:

**nicevpccentos -> niceclassiccentos**

```bash
ping 10.85.198.16
```

Results:

```bash
# ping 10.85.198.16
PING 10.85.198.16 (10.85.198.16) 56(84) bytes of data.
64 bytes from 10.85.198.16: icmp_seq=1 ttl=55 time=1.66 ms
64 bytes from 10.85.198.16: icmp_seq=2 ttl=55 time=1.03 ms
64 bytes from 10.85.198.16: icmp_seq=3 ttl=55 time=1.02 ms
64 bytes from 10.85.198.16: icmp_seq=4 ttl=55 time=1.07 ms
```

 **nicevpccentos  -> niceclassicwin**

```bash
ping 10.85.198.25
```

Results:

```bash
# ping 10.85.198.25
PING 10.85.198.25 (10.85.198.25) 56(84) bytes of data.
64 bytes from 10.85.198.25: icmp_seq=1 ttl=119 time=1.81 ms
64 bytes from 10.85.198.25: icmp_seq=2 ttl=119 time=1.31 ms
64 bytes from 10.85.198.25: icmp_seq=3 ttl=119 time=1.24 ms
64 bytes from 10.85.198.25: icmp_seq=4 ttl=119 time=1.37 ms
64 bytes from 10.85.198.25: icmp_seq=5 ttl=119 time=1.31 ms
64 bytes from 10.85.198.25: icmp_seq=6 ttl=119 time=1.34 ms
```



### test4:  From VPC Windows VSI to Classic VSIs

Now we connect to  the VPC Windows VSI. 

We need to  add  a floating IP to the VPC Windows VSI. 

To do it, go to  the description of the VSI in the VPC section:

![image-20210227153607789](images/image-20210227153607789-4436567.png)



At the bottom of the  section, find the network interfaces :

![image-20210227152243051](images/image-20210227152243051-4435763.png)

To add  a **floating IP**, click on the **pen** on the right side of the line and change the button to **Reserve  a new floating IP** like below:

![image-20210227152439493](images/image-20210227152439493-4435879.png)

Then **Save** your change.

![image-20210227152616439](images/image-20210227152616439-4435976.png)

Take a note of the **floating IP.** 

Before you can connect to this floating IP, be sure that the **security group** that is associated with the instance allows inbound and outbound Remote Desktop Protocol traffic (TCP port 3389). In my case I authorized all **ingress** for any protocol (not to be  put in production)

![image-20210227224359951](images/image-20210227224359951-4462239.png)

The second point is that you need to obtain the **password** for the Administrator. 

On your laptop, use the following command to decrypt the password:

```
ibmcloud is instance-initialization-values INSTANCE_ID [--private-key (KEY | @KEY_FILE)]
```

This command decodes and decrypts your password, which is automatically generated when you create an instance using a Windows image. The password is decoded and decrypted based on the public SSH Key that you used at instance create time and the associated private SSH key that you specify in this `instance-initialization-values` command. 

In my case, here is the command:

``` bash
ibmcloud is instance-initialization-values 02c7_55339b38-357c-4bd1-a771-7717dae9cd27 --private-key @~/test777
```

Results:

```bash
# ibmcloud is instance-initialization-values 02c7_55339b38-357c-4bd1-a771-7717dae9cd27 --private-key @~/test777
Getting instance initialization values for 02c7_55339b38-357c-4bd1-a771-7717dae9cd27 under account Kevin Gilhooly's Account as user ph_thomas@fr.ibm.com...
Keys
ID                                          Name   
r010-5f518c92-19a5-44a2-ab8c-74231c1a55af   test777   
              
Password   892kYCe51zvgS4df7yfL   
Key        ID                                          Name      
           r010-5f518c92-19a5-44a2-ab8c-74231c1a55af   test777    
```

In my example the **Administrator** password is **892kYCe51zvgS4df7yfL**



Use **RDP** to access into the Windows VPC VSI with the **floating IP.**

![image-20210227153938049](images/image-20210227153938049-4436778.png)



This results in connection to the Windows Console:

![image-20210227224621771](images/image-20210227224621771-4462381.png)



Open a **terminal** console:

![image-20210227224737205](images/image-20210227224737205-4462457.png)



Then ping the Classic VSIs:

**nicevpcwin -> niceclassiccentos**

```bash
ping 10.85.198.16
```

Results:

```bash
# ping 10.85.198.16
PING 10.85.198.16 (10.85.198.16) 56(84) bytes of data.
64 bytes from 10.85.198.16: icmp_seq=1 ttl=55 time=1.66 ms
64 bytes from 10.85.198.16: icmp_seq=2 ttl=55 time=1.03 ms
64 bytes from 10.85.198.16: icmp_seq=3 ttl=55 time=1.02 ms
64 bytes from 10.85.198.16: icmp_seq=4 ttl=55 time=1.07 ms
```

 **nicevpcwin  -> niceclassicwin**

```bash
ping 10.85.198.25
```

Results:

```bash
# ping 10.85.198.25
PING 10.85.198.25 (10.85.198.25) 56(84) bytes of data.
64 bytes from 10.85.198.25: icmp_seq=1 ttl=119 time=1.81 ms
64 bytes from 10.85.198.25: icmp_seq=2 ttl=119 time=1.31 ms
64 bytes from 10.85.198.25: icmp_seq=3 ttl=119 time=1.24 ms
64 bytes from 10.85.198.25: icmp_seq=4 ttl=119 time=1.37 ms
64 bytes from 10.85.198.25: icmp_seq=5 ttl=119 time=1.31 ms
64 bytes from 10.85.198.25: icmp_seq=6 ttl=119 time=1.34 ms
```



---

This is the end of this document.







#END OF LAB