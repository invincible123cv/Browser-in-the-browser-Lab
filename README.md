<h1>Browser-in-the-browser-Lab</h1>

<h2>Description</h2>
In this browser attack simulation project, we set up a cloud-hosted server and install a web browser such as Chrome or Firefox on it. The browser is then used to load the login or authentication page of a target service (for example, Facebook, Gmail, or Instagram). Next, we configure remote access so that this browser session can be accessed through a unique URL, which is then shared with the intended target. Because the victim interacts with a legitimate website rendered on the cloud-hosted browser rather than their own device, authentication tokens and session data are handled directly within that environment, making it possible to bypass protections such as two-factor or multi-factor authentication in a simulated attack scenario.
<br />


<h2>Languages and Utilities Used</h2>

- <b>Linux Command Line Interface</b> 
- <b>Docker</b>

<h2>Environments Used </h2>

- <b>Azure Cloud</b>

<h2>Install the web browser on the cloud server</h2>

<h3> Install docker on the VM</h3>

<p>First create a Resource Group in Azure then create a virtual machine with the latest Ubuntu Server as the OS image</p>

<p align="center">
<img src="" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Select the disk:  <br/>
<img src="https://i.imgur.com/tcTyMUE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />

</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
