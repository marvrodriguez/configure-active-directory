![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/cb547ff3-073e-4a5a-978c-23c2cf5ec39e)


<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Domain Controller VM (Windows Server 2022) named "DC-1"
- Domain Controller's NIC Private IP address to be static
- ICMPv4 (ping) was allowed on the Domain Controller
- Create an Admin and Normal User Account in Active Directory
- Join Client to domain
- Attempt to login CLient-1 with one of the users

<h2>Deployment and Configuration Steps</h2>

<details>
<summary>
  
#### Creating Domain Controller VM "DC-1" and Client-1 VM
 </summary>

First we're going to create 2 Virtual Machines in Azure, one to serve as our Domain Controller VM named "DC-1" and the other to be our Client VM named "Client-1"
 
![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/fd49432d-30dc-4794-9333-ee3ed1881444)


![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/27689a82-e69c-4c35-863f-1b84935a8a75)

While creating the Client VM head over to the networking page, we need to check that the CLient VM is on the same Virtual Network as the Domain Controller.

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/8dc2ea62-18fe-47b8-a5c2-8395722238fb)

</details>

<details> 
<summary> 
  
#### Changing the Domain Controller's NIC private IP address from dynamic to static
   </summary>
Head back to Virtual Machines and click DC-1, once opened beneath the Settings open the networking option and click the Netowrk intrface link.

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/629c2222-1052-4950-b88a-40b6e23c2c88)

Once on the Network Interface page, beneath the settings click on IP configurations, as you can see the Private IP address of our Domain Controller is Dynamic which we have to change to Static.

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/157ef63c-54df-4f7f-87b0-9fcfa3f936e2)

To change the IP address from Dyanmic to Static click on ipconfig1 to bring up the settings.

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/23c07ee4-c4b7-4e56-859e-f1245d22d484)

</details>


<details>
  <summary>
    
#### Ensure Connectivity between the Client and Domain Controller

</summary> 
To check connectivity first login to Client1 via Remote Desktop and ping DC-1's IP address with ping-t. Head back to the virtual machine to grab DC-1's Private IP address.

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/00268de0-ab6e-4077-8411-67962b3bc80c)


Once logged in to CLient-1 open Command Prompt and ping DC-1's Private IP address and leave the window open as we'll come back to check on the status.

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/56687de1-ea20-4872-bd02-d630ae491e89)

As you can see the ping is being dropped/fail

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/13879c6d-f958-490f-871a-ad874024b795)

To make a connection from Client1 to DC-1, we need to login to DC-1 via remote desktop and enable ICMPv4 on the local windows firewall.
Once logged in to DC-1 on the search bar type firewall, and pick windows defender firewall with advanced security.

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/ae1ecaa4-5ab1-41d6-b33d-97b20f23af7e)

On the windows defender firewall click inbound rules, sort by Protocol and scroll down and find ICMPv4 and enable rule for both Core Networking Diagnostics.

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/b817f86e-a971-4305-8c63-b3b8d94f9efc)

Once the rules have been enabled, head back to Client-1 and check the command prompt and check if the ping started to work.

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/65bc128a-0c65-4d83-94eb-58f5a5baf0c8)
</details>

<details> 
<summary> 
  
#### Install Active Directory
</summary>
While in DC-1 open Server Manager and click Add roles and features, once you reach the Server Roles check Active Directory Domain Services and click add features and finish the installation.

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/5153c949-6ffb-40d7-abf3-c91aff048ceb)

After the installation close the window and click the flag on the top right with an indication, here we will promote the server to a domain controller, add a new forest as mydomain.com setup. Remote Desktop was Restarted and logged back into DC-1 as user: mydomain.com\labuser.

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/cdd12d92-13f7-4e6e-aa1b-55be71e6dc79) ![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/30c559fd-b117-4bd2-be3a-eb5d85a4e914)

 </details>


<details>
  <summary> 
    
#### Create an Admin and Normal User Account in Active Director
   </summary>
While in the Server Manager on the top right corner click on tools and click on Active Directory Users and Computers it's here where we will create an OU (Organizational Unit) for "EMPLOYEES" and "ADMINS"

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/8dfb267a-5e76-4346-a10a-6da28bf1080c)

To create a new OU, right click on mydomain.com -> New -> Organizational Unit

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/1217bd8c-93bd-43e3-867e-33128ac81f59)

Once the new OU's have been created open the OU named ADMINS and create a new user named "Jane Lee" with the username of "jane_admin" by right clicking -> New -> User
Now we have to make Jane Lee into an admin to do so we need to add the user to the "domain admins" security group, right click the User and head to properties -> Member of -> Add -> Enter the objec names to select type in "domain" -> Check names

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/656d3575-08e6-469b-a7df-4848459e039e)

Click Domain Admins and apply changes, Log out/close the connection to DC-1 and log back in as "mydomain.com\jane_admin"

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/2341bc9a-877f-4d26-97f6-b0a3246304c2)

Log out/close the connection to DC-1 and log back in as "mydomain.com\jane_admin"

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/89def9af-b49b-4a86-a72d-4ad3c4de48c8)

 </details>

<details> 
  <summary> 
    
#### Join Client-1 to your domain (mydomain.com)
  </summary>
For Client-1 to join the domain, we need to change the DNS server of client-1 to the private IP address of DC-1, from the Azure portal head over to Client-1's VM and head to networking and click on Client-1's NIC.

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/910da2a3-f3b4-4cf6-817a-021b9e5ad99c)

Once on Client-1s NIC settings, find DNS servers, by default the option will be to inherit from virtual network, change it to custom and type in DC-1's private IP address

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/c1931b14-d383-47a5-bc72-907653e1e780)

As soon as its done updating, head back to Client-1's Overview and click Restart.

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/8784e67d-2adc-46ca-86a5-0e98e2d83cc3)

Now that Client-1 has been restarted, log back in so we can join it to the domain, once logged in, right click the start menu and click on System to bring out the System Settings,from there click on Rename this PC (advanced)

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/518fbad3-2e38-4e63-be8e-b8cd471a03cd)

Under the Computer Name tab, Click on Change to rename the computer name or its domain or workgroup, from there under the Member of click domain and type in mydomain.com 

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/c2a6a65b-3979-4be6-b1ac-514cce053b42)

When credentials are asked for just enter the local admin account or mydomain.com\jane_admin with the password and Client-1 will be joined to the domain.

![image](https://github.com/marvrodriguez/configure-active-directory/assets/141983161/a992b2f7-d89e-4973-9c46-9d975cd68b16)
</details>
