<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

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

- Create a resource group, virtual network and subnet within azure
- Create 2 virtual machines, one acting as a DNS server
- Install Active Directory on DM-1
- Create Domain Admin user and join Client-1 to the Domain

<h2>Deployment and Configuration Steps</h2>

<p>
  <h2>1. Create a resource group, virtual network, subnet and Domain controller virtual machine within Azure</h2>

  <img width="935" alt="image" src="https://github.com/user-attachments/assets/2c569e33-81a0-46b0-82ea-bb2cd7853de7" />

</p>
<p>
Go to reasource groups and then create, name it Active Directory and then press create. Got to virtual network, create and out it in the created resource group. Name it Active-Directory-Vnet and create. Now go to Virtual Machines and press create, put it in the same resource group as before and make sure the region is the same as the one you used for the virtual network. Name it DC-1 and for image choose Windows Server 2022. For size make sure to pick something with atleast 2 vcpus and set the username and password to your liking. Click next until you get to networking and choose the virtual network we created and then press create.
</p>
<br />

<p>
  <h2>2. Create a second virtual machine and configure its DNS settings</h2>
  
<img width="934" alt="image" src="https://github.com/user-attachments/assets/6b4d2e05-7ddd-44c4-9846-5c5e7e890414" />

</p>
<p>
Go to virtual machines and create a second virtual machine named Client-1, put it in the resource group you created and set the image as Windows 10 pro. You can use the same size that you did for the previous VM. Put the same username and password as you did for the first VM for ease of use and then click licensing agreement. Click next until you get to networking and out it in the network we created then create the VM. To set Client-1’s DNS settings to DC-1’s Private IP address click on DC-1 under virtual machines and copy its private IP address. Click on Client-1 and head to network settings. Click on the NIC and then DNS servers. Click custome and then paste it in the DNS server box and then save it, finally click on CLinet-1 under virtual machines and restart it.
</p>
<br />

<p>
   <h2>3. Set Domain Controller’s NIC Private IP address to be static and disable the Windows Firewall</h2>

<img width="940" alt="image" src="https://github.com/user-attachments/assets/6436be6c-bddc-4980-a82b-2cabbad2291a" />

</p>
<p>
Click DC-1 under virtual machines, go to Network Settings and then click on the NIC and then ipconfig1. Under Private IP Address Settings change it to static and then save it. This makes sure the IP address doesn't change no matter how many times the VM is restarted. Now we will disable the windows firewall for testing purposes. Log in to the DC-1 VM with remote desktop, you will use the username and password you created it with. Once it boots up, right click the start menu, click run and type in mf.msc. Click windows Defender Firewall Properties and then press O to turn off the firewall on Domain Profile, Private Profile and Public Profile then click ok.
</p>
<br />

<h2>3. Test to see if it worked</h2>

![image](https://github.com/user-attachments/assets/9d96ca1a-0523-40e4-a4ee-6ada491beb09)

</p>
<p>
Log in to Clinet-1 VM through remote desktop, type in power shell in the search bar and open it. Type in "ping 10.0.0.4" and if it receives all the packets then it was a success. Now run ipconfig /all and check DNS servers which should be 10.0.0.4 or whatever Private Ip addresss your DC-1 VM had. Now you know you've done it correctly. 
<br />

<h2>4. Install Active Directory </h2>

![image](https://github.com/user-attachments/assets/390f9b17-d8c5-4286-8a97-329d5a2531b7)

</p>
<p>
Log into the DM-1 VM with Remote Desktop. Click on start and then server manager. Add roles and features, click next until Server roles and click "Active Directory Domain Services" click next and then next, check the box "Restart the destination server automatically if required" and then click install. Now we are going to configure DC-1 to make it a domain controller. From the server Manager, click on the flag shown in the photo and click on "promote this server to a domain controller". Click add new forrest and type mydomain.com and then next. Put an easy password and then next and uncheck the "Specify DNS delegation options" box. Click next until you can press intsall and install it. Log back in with mydomain.com\username as the username from now on. 
<br />

<h2>4. Create a Domain Admin user within the domain</h2>

![image](https://github.com/user-attachments/assets/6f8aa46c-1fb1-4a14-96f0-e0958265e6ea)

</p>
<p>
In DM-1 click start and go to Windows Adminstrative Tools > Active Directory Users and Computers. Right click mydomain.com then click new > Organizational unit and name it “_EMPLOYEES”. Create another one named "_ADMINS". Now we're going to create a new employee. Go to the Admins folder and right click to create new User. Name her Jane Doe and set the username as Jane_admin and password what you want then create it. Now right click the new user, go to Properties, member of, new and type domain admins and click check name. Press ok, apply, ok, no Jane is a domain admin. Now log off and log back in as Jane admin and we will be using her account from now on.
<br />

<h2>5. Join Client-1 to your domain (mydomain.com)</h2>

![image](https://github.com/user-attachments/assets/dcd96eb8-2138-415f-a0f4-da6d1f8a4517)

</p>
<p>
Login to Client-1 as original local admin. Right click start menu and press System. Click rename this PC advanced, click change and type in mydomain.com in the Domain box under Member of. Click apply and then ok and it will restart your VM. Now we're going to verify client-1 shows up in the ADUC. Login into the domain controller and go to Active Directory Users and Computers like before, click computers and you should see Client-1 which means you did it correctly. 
<br />

