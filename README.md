# kodi-raspberrypi-mediacenter
Comprehensive guide to setting up a Raspberry Pi media center with LibreELEC and Kodi, using Docker for torrent automation with qBittorrent, Jackett, Sonarr, and Radarr. Learn how to configure external storage, manage subtitles, and organize your media library for a seamless 4K home entertainment experience.

## Hardware Requirements

To set up the system, you'll need the following hardware components:

- **Raspberry Pi 5 (8GB)**  
  The latest model of Raspberry Pi provides sufficient power for running Docker, Kodi, and additional services without performance issues.

- **Official Raspberry Pi Power Supply (USB-C, 27W)**  
  It is important to use the official power supply to ensure stable operation of the Raspberry Pi 5, especially when using peripherals like external hard drives.

- **Official Raspberry Pi Case with Fan**  
  This case will keep your Raspberry Pi cool during heavy workloads, and the included fan helps prevent overheating.

- **4TB External Hard Drive**  
  For storing media files, it's recommended to use a large-capacity external hard drive, such as a 4TB drive, which will be used to store all your downloaded media and allow easy integration with Docker services.

- **128GB microSD Card (Class 10 or higher)**  
  The microSD card will be used to install Raspberry Pi OS or LibreELEC. Ensure it's a high-speed card to avoid bottlenecks.

- **Micro-HDMI to HDMI Cable (1 meter)**  
  This cable is used to connect your Raspberry Pi to a monitor or TV for setup and visual output.

- **Ethernet Cable**  
  If you're using a wired connection, you will need an Ethernet cable to connect your Raspberry Pi to your local network.

Ensure that all components are available before beginning the setup process.

## Preparing the microSD Card and Installing LibreELEC

### 1. Format the microSD Card

Before installing LibreELEC, you need to format the microSD card. You can do this on a Linux or macOS system using the following commands:

#### For macOS

1. Insert the microSD card into your Mac using an adapter (if necessary).
   
2. Open **Disk Utility** (Applications > Utilities > Disk Utility).

3. Select the microSD card from the list on the left.

4. Click on **Erase** at the top of the window.

5. In the format drop-down menu, select **exFAT**.

6. Give the card a name (e.g., "RaspberryPi") and click **Erase**.

7. Once the process is complete, eject the card safely.

### 2. Download the Raspberry Pi Image

1. Visit the official Raspberry Pi website to download the **Raspberry Pi Imager** tool:
   - Go to [https://www.raspberrypi.org/software/](https://www.raspberrypi.org/software/).

2. Download the **Raspberry Pi Imager** for your operating system

3. Once downloaded, open the installer and follow the on-screen instructions to install **Raspberry Pi Imager** on your Mac.

4. After installation, open the **Raspberry Pi Imager**.

### 3. Install LibreELEC on the microSD Card

1. In the Imager application, follow these steps:
   - **Choose Operating System**: Click on **CHOOSE OS**, then navigate to **Media Player OS > LibreELEC > LibreELEC (RPI5)** to select the LibreELEC image for the Raspberry Pi 5.
   - **Choose Storage**: Click on **CHOOSE STORAGE** and select your microSD card (make sure it is properly inserted).
   - **Choose Device**: Make sure you have selected **Raspberry Pi 5** as the device.

2. Once everything is set, click **WRITE** to begin the installation process. The Raspberry Pi Imager will download and write the LibreELEC image to your microSD card.

3. Wait for the process to complete. Once finished, safely eject the microSD card from your Mac.

Now your microSD card is ready with **LibreELEC** for the Raspberry Pi 5.

## Accessing the Raspberry Pi Remotely

To access your Raspberry Pi remotely, you first need to set up SSH (Secure Shell). Here's how to do it:

1. **Connect the Raspberry Pi to a Display and Keyboard**:
   - Connect your Raspberry Pi to a monitor via HDMI.
   - Connect a keyboard to the Raspberry Pi.
   - Plug in an Ethernet cable to ensure the Raspberry Pi has network access.

2. **Power on the Raspberry Pi**:
   - Turn on the Raspberry Pi and wait for it to boot into LibreELEC.

3. **Enable SSH**:
   - Once LibreELEC has booted, navigate to the **LibreELEC Settings**:
     - Open the settings by going to the **LibreELEC Settings Menu**.
     - In the **Network** section, look for an option called **SSH** (or "Secure Shell").
     - Enable SSH and set a strong password for SSH access. This will be used to log in remotely.

4. **Find the Raspberry Pi's IP Address**:
   - To connect via SSH, you need to know the Raspberry Pi's IP address.
   - You can find this by going to **System Information** in the settings menu or using a network tool to detect the IP address of the Raspberry Pi on your network.

5. **Connect Remotely via SSH**:
   - From your computer, open a terminal or command prompt.
   - Use the following SSH command to connect to your Raspberry Pi:
     ```
     ssh root@<raspberry_pi_ip_address>
     ```
   - Replace `<raspberry_pi_ip_address>` with the actual IP address of the Raspberry Pi.

6. **Enter Password**:
   - When prompted, enter the password you set earlier for SSH access.
   - You should now be logged in to the Raspberry Pi remotely and be able to control it via command line from your computer.

Now you can access your Raspberry Pi remotely using SSH from your computer, making it easier to manage and configure it without needing to use a display or keyboard directly attached to the device.

---

## Configuring the External Hard Drive

To use an external hard drive for storage in LibreELEC, you need to mount it properly. Follow these steps:

### 1. Ensure You Are Connected to the Raspberry Pi  
   - Make sure you are connected to the Raspberry Pi remotely via SSH, as described in the previous section.

### 2. Identify the External Hard Drive  

1. **Connect Your Hard Drive**  
   - Attach your external hard drive to one of the USB ports on the Raspberry Pi.

2. **List All Storage Devices**  
   - Run the following command to display all available storage devices and their details:  
     ```
     blkid
     ```
   - You will see a list of devices like `/dev/sda1`, `/dev/sdb1`, etc. Look for the entry corresponding to your external hard drive.  

3. **Check for Multiple Partitions**  
   - If your hard drive has more than one partition, identify the largest one. This is typically where the main storage is located.  

4. **Format the Partition (if needed)**  
   - If the partition is not already formatted as exFAT, you must reformat it. Note that formatting will erase all data on the partition, so ensure any important files are backed up.  
   - Run the following commands to format the partition to exFAT:  
     ```
     mkfs.exfat /dev/sdX1
     ```
     Replace `/dev/sdX1` with the correct partition name, such as `/dev/sda1`.

Now, you are ready to proceed with mounting the drive and setting up the necessary directories.


### 3. Create a Mount Point  
   - You need to create a directory where the hard drive will be mounted. For example, to create a folder named `external_disk` under `/storage`, run:  
     ```
     mkdir /storage/external_disk
     ```

### 4. Mount the Hard Drive  
   - Use the following command to mount the external drive to the directory you just created:
     ```
     mount /dev/sda1 /storage/external_disk
     ```
   - Replace `/dev/sda1` with the device name of your hard drive.

### 5. Verify the Mount  
   - To check if the drive has been mounted successfully, use this command:
     ```
     df -h
     ```
   - You should see your external drive listed with the mount point `/storage/external_disk`.  
   
### 6. Optional: Automate Mounting at Boot  
   - To ensure the drive is mounted automatically on startup, you can add an entry to LibreELEC's storage configuration file:
     1. Open the configuration file with a text editor:
        ```
        nano /storage/.config/autostart.sh
        ```
     2. Add the following line:
        ```
        mount /dev/sda1 /storage/external_disk
        ```
     3. Save and exit the file (`CTRL+O`, then `CTRL+X`).

Your external hard drive is now mounted and ready for use. You can store your media files here and configure other tools, like Sonarr or Radarr, to use this drive as their storage location.

---

## Setting Up Directories for Docker Containers

To ensure proper organization and functionality for the Docker containers, you need to create directories for configuration files, downloads, and media storage. This structure keeps everything well-organized and easy to manage.

### 1. Navigate to the External Drive Mount Point  
   - First, navigate to the mount point of your external hard drive:
     ```
     cd /storage/external_disk
     ```

### 2. Create Required Directories  
   - Run the following commands to create the necessary directories:
     ```
     mkdir -p docker/config
     mkdir -p downloads/movies-radarr
     mkdir -p downloads/tv-sonarr
     mkdir -p media/movies
     mkdir -p media/tv
     ```

### 3. Explanation of the Directories  
   - **`docker/config`:** Stores configuration files for all Docker containers, such as settings for qBittorrent, Sonarr, Radarr, and Jackett.  
   - **`downloads`:** Temporary storage for media files downloaded by the containers.  
     - **`movies-radarr`:** For movies downloaded by Radarr.  
     - **`tv-sonarr`:** For TV shows downloaded by Sonarr.  
   - **`media`:** Final destination for organized media files:  
     - **`movies`:** For properly imported and sorted movies.  
     - **`tv`:** For properly imported and sorted TV shows.

### 4. Set Permissions for the Directories  
   - Ensure the Docker containers have full access by granting the appropriate permissions:
     ```
     chmod -R 777 docker downloads media
     ```

Your directories are now ready for use. This setup ensures a clean separation between configurations, downloads, and organized media, making it easier to manage your system.

---

## Installing Docker on LibreELEC  

1. **Install Docker Add-On via Kodi**  
   - Open Kodi on your Raspberry Pi.  
   - Navigate to `Add-ons` > `Install from repository`.  
   - Select `LibreELEC Add-ons` > `Services` > `Docker`.  
   - Click `Install` and wait for the Docker add-on to be installed.  

2. **Verify the Docker Installation**  
   - Open an SSH connection to your Raspberry Pi (refer to **Access the Raspberry Pi Remotely** if needed).  
   - Check the Docker version to ensure it is installed correctly by running:  
     ```bash
     docker --version
     ```  
   - You should see an output similar to:  
     ```
     Docker version XX.XX.XX, build XXXXXX
     ```  

Now that Docker is installed and verified, you can proceed to set up your containers for applications like qBittorrent, Sonarr, Radarr, and more.

---

## Setting Up and Running Docker Containers  

Now that Docker is installed, we can set up the containers needed for our media center. We will create a Docker network for better container communication and then deploy the following containers:  

- **qBittorrent**: For downloading torrent files.  
- **Jackett**: Acts as an indexer proxy for qBittorrent.  
- **Sonarr**: For managing and automating TV show downloads.  
- **Radarr**: For managing and automating movie downloads.  

### 1. Create a Docker Network  
First, create a dedicated Docker network to allow all containers to communicate seamlessly:  
```bash
docker network create media-network
```
### 2. Deploy Containers

#### **qBittorrent**  
This container handles torrent downloads.  
```bash
docker run -d \
  --name qbittorrent \
  --network media-network \
  -p 8080:8080 \
  -p 8999:8999 \
  -v /storage/external_disk/config/qbittorrent:/config \
  -v /storage/external_disk/downloads:/downloads \
  -e PUID=1000 -e PGID=1000 \
  --restart unless-stopped \
  linuxserver/qbittorrent
```
#### **Jackett**  
Jackett integrates with qBittorrent to provide access to multiple torrent trackers.  

```bash
docker run -d \
  --name jackett \
  --network media-network \
  -p 9117:9117 \
  -v /storage/external_disk/config/jackett:/config \
  -v /storage/external_disk/downloads:/downloads \
  -e PUID=1000 -e PGID=1000 \
  --restart unless-stopped \
  linuxserver/jackett
```
#### **Sonarr**  
Sonarr is used to manage and download TV shows, automating the process of finding and organizing episodes.  

```bash
docker run -d \
  --name sonarr \
  --network media-network \
  -p 8989:8989 \
  -v /storage/external_disk/config/sonarr:/config \
  -v /storage/external_disk/media/tv:/tv \
  -v /storage/external_disk/downloads:/downloads \
  -e PUID=1000 -e PGID=1000 \
  --restart unless-stopped \
  linuxserver/sonarr
```
#### **Radarr**  
Radarr is used to manage and download movies, automating the process of finding and organizing them.  

```bash
docker run -d \
  --name radarr \
  --network media-network \
  -p 7878:7878 \
  -v /storage/external_disk/config/radarr:/config \
  -v /storage/external_disk/media/movies:/movies \
  -v /storage/external_disk/downloads:/downloads \
  -e PUID=1000 -e PGID=1000 \
  --restart unless-stopped \
  linuxserver/radarr
```
### 3. Verify the Containers
   Run the following command to ensure all containers are up and running:  
   ```bash
   docker ps
   ```
---
## Configuring initial Setup

### Sonarr

#### Step 1: Access Sonarr
1. Open your browser and navigate to the IP address of your Raspberry Pi with the Sonarr port (default: `http://<raspberry_pi_ip>:8989`).
   Example: `http://192.168.1.100:8989`.

2. You'll see the Sonarr dashboard. If it’s your first time accessing it, follow the setup wizard or proceed with the manual setup below.

#### Step 2: Set Up Authentication
1. Navigate to **Settings** > **General**.
2. In the **Security** section:
   - Set a **Username** and **Password** for accessing Sonarr.
   - Enable the option **Bypass Authentication for Local Addresses** to allow access without authentication from localhost.
   - Click **Save** to apply the changes.

#### Step 3: Configure Root Folder
1. Go to **Settings** > **Media Management**.
2. In the **Root Folders** section:
   - Click **Add Root Folder**.
   - Set the root folder to `/media/tv` (this is the path where your TV series will be stored).
   - Save the configuration.

---

### Radarr

#### Step 1: Access Radarr
1. Open your browser and navigate to the IP address of your Raspberry Pi with the Radarr port (default: `http://<raspberry_pi_ip>:7878`).
   Example: `http://192.168.1.100:7878`.

2. You'll see the Radarr dashboard. If it’s your first time accessing it, follow the setup wizard or proceed with the manual setup below.

#### Step 2: Set Up Authentication
1. Navigate to **Settings** > **General**.
2. In the **Security** section:
   - Set a **Username** and **Password** for accessing Radarr.
   - Enable the option **Bypass Authentication for Local Addresses** to allow access without authentication from localhost.
   - Click **Save** to apply the changes.

#### Step 3: Configure Root Folder
1. Go to **Settings** > **Media Management**.
2. In the **Root Folders** section:
   - Click **Add Root Folder**.
   - Set the root folder to `/media/movies` (this is the path where your movies will be stored).
   - Save the configuration.

---

### qBittorrent

#### Step 1: Retrieve Default Password from Container Logs
1. Access the terminal on your Raspberry Pi.
2. Run the following command to view the logs of the qBittorrent container:
   ```bash
   docker logs qbittorrent
   ```
3. Look for a line in the logs containing the default web UI password. It typically appears as:
   ```vbnet
   Default password is: <password>
   ```
   Take note of the password.

#### Step 2: Log in to the qBittorrent Web UI
1. Open your browser and navigate to the qBittorrent web UI: `http://<raspberry_pi_ip>:8080`. Example: `http://192.168.1.100:8080`.
2. Log in using:
   - Username: admin
   - Password: The default password retrieved in Step 1.

#### Step 3: Change the Default Password
1. Once logged in, go to **Tools** > **Options** > **Web UI**.
2. Under the Authentication section:
   - Enter a new password in the Password field and confirm it in the Confirm Password field.
   - Click Apply to save the new password.

#### Step 4: Set the Default Download Folder
1. In the qBittorrent settings, navigate to Downloads.
2. Under the Default Save Path section:
   - Set the path to /downloads.
   - Click Apply to save the changes.

#### Step 5: Create Categories for Radarr and Sonarr 
1. Go to the Categories tab in qBittorrent.
2. Create the following categories:
   - **movies-radarr**:
     - Set the save path to /downloads/movies-radarr.
   - **tv-sonarr**:
     - Set the save path to /downloads/tv-sonarr.
3. Click Apply to save the categories.

---

### Jackett

#### Step 1: Access the Jackett Web UI
1. Open your browser and navigate to the Jackett web UI:
   `http://<raspberry_pi_ip>:9117`.
   Example: `http://192.168.1.100:9117`.
2. Once loaded, you should see the Jackett dashboard.

#### Step 2: Add an Indexer
1. On the Jackett dashboard, click the **Add Indexer** button.
2. Use the search bar to find your preferred tracker or indexer.
3. Click the **+** (plus) button next to the tracker you want to add.

#### Step 3: Configure the Indexer
1. After clicking the **+** button:
   - If the tracker requires login credentials (for private trackers), provide your **username** and **password** or any required API key.
   - Save the configuration.
2. Once added, the indexer should appear in the list of active indexers.

#### Step 4: Test the Indexer
1. On the Jackett dashboard, find your newly added indexer.
2. Click the **Test** button next to it to ensure it’s working correctly.
3. If the test is successful, the indexer is ready for use.

#### Step 5: Save and Exit
1. After confirming everything works, close the Jackett web UI or leave it running in the background.
2. Your Jackett configuration is now complete, and it’s ready to be linked with Radarr and Sonarr.

This completes the initial setup.

---

## Connecting Sonarr and Radarr with qBittorrent and Jackett

In this section, we will configure Sonarr and Radarr to connect with qBittorrent for downloading content and with Jackett for accessing indexers.


### **1. Adding qBittorrent to Sonarr and Radarr**

1. **Open Sonarr and Radarr** in your browser.
   - Sonarr: `http://<your_raspberry_pi_IP>:8989`
   - Radarr: `http://<your_raspberry_pi_IP>:7878`

2. **Navigate to the Download Client settings:**
   - In Sonarr or Radarr, go to **Settings > Download Clients**.
   - Click on the **+** button to add a new download client.

3. **Configure qBittorrent:**
   - Select **qBittorrent** from the list.
   - Fill out the form with the following details:
     - **Host:** `qbittorrent`
     - **Port:** `8080`
     - **Username:** (your qBittorrent username)
     - **Password:** (your qBittorrent password)
   - Click **Test** to ensure the connection works.
   - Click **Save**.

---

### **2. Adding Jackett Indexers to Sonarr and Radarr**

#### **Step 1: Get the Indexer URL and API Key from Jackett**
1. **Access Jackett:**
   - Open Jackett in your browser: `http://<your_raspberry_pi_IP>:9117`.

2. **Copy the Torznab Feed URL:**
   - Find an indexer you have added in Jackett and click **Copy Torznab Feed**.
   - The copied URL will look like this:
     ```
     http://<your_raspberry_pi_IP>:9117/api/v2.0/indexers/<indexer_name>/results/torznab/
     ```
   - Replace `<your_raspberry_pi_IP>` with `jackett` to take advantage of the Docker network:
     ```
     http://jackett:9117/api/v2.0/indexers/<indexer_name>/results/torznab/
     ```

3. **Get the API Key:**
   - In Jackett, go to **API Key** in the top-right corner of the interface.
   - Copy the key for use in Sonarr and Radarr.

#### **Step 2: Add the Indexer to Sonarr and Radarr**
1. **Open Sonarr or Radarr** and go to **Settings > Indexers**.
2. **Add a New Indexer:**
   - Click **+** and select **Torznab**.
3. **Configure the Torznab Indexer:**
   - **Name:** Enter a friendly name for the indexer.
   - **URL:** Paste the Torznab Feed URL (modified to use `jackett` instead of the IP address).
   - **API Key:** Paste the API Key copied from Jackett.
   - **Categories:**
     - For **Sonarr**, use the category: `5000, 5070`.
     - For **Radarr**, use the category: `2000, 2010`.
   - Click **Test** to ensure the connection works.
   - Click **Save**.

---

### **3. Testing the Setup**

1. **Search for Titles:**
   - In Sonarr or Radarr, go to **Add New Series** or **Add New Movie**.
   - Search for a title and add it to your library.

2. **Verify Download Client Integration:**
   - When a title is searched, Sonarr or Radarr will use the Jackett indexer to find torrents and send them to qBittorrent.

3. **Check qBittorrent:**
   - Open qBittorrent and verify that the download appears under the correct category:
     - **TV-Sonarr**: Downloads TV series.
     - **Movies-Radarr**: Downloads movies.

---

## **Configuring Kodi for Movies and TV Shows**

Follow these steps to configure Kodi to read from your media folders and download metadata for movies and TV shows:


#### **1. Set Up the Movies Section**

1. **Open Kodi** and navigate to:
   - **Settings (Gear Icon) > Media Settings > Library**.
2. Select **Videos**, then click **Add Videos...**.
3. In the dialog box that appears:
   - **Browse** to the folder where your movies are stored: `/media/movies`.
   - Click **OK** to confirm.
4. Enter a name for this source, e.g., `Movies`, and click **OK**.
5. In the next screen:
   - Select **This directory contains: Movies**.
   - Choose a scraper for metadata (e.g., **The Movie Database**).
   - Click **OK**.
6. In the confirmation prompt, select **Yes** to scan the folder and download metadata for your movies.

---

#### **2. Set Up the TV Shows Section**

1. Go back to **Settings > Media Settings > Library**.
2. Select **Videos**, then click **Add Videos...**.
3. In the dialog box that appears:
   - **Browse** to the folder where your TV shows are stored: `/media/tv`.
   - Click **OK** to confirm.
4. Enter a name for this source, e.g., `TV Shows`, and click **OK**.
5. In the next screen:
   - Select **This directory contains: TV Shows**.
   - Choose a scraper for metadata (e.g., **The TVDB** or **The Movie Database**).
   - Click **OK**.
6. In the confirmation prompt, select **Yes** to scan the folder and download metadata for your TV shows.

---

#### **3. Enable Automatic Updates for New Content**

1. Go to **Settings > Media Settings > Library**.
2. Under **Library Update Options**, enable the following:
   - **Update library on startup**: Automatically scans for new media when Kodi is launched.
   - **Show library updates progress**: Displays a progress bar during updates.

---

#### **4. Verify Metadata Downloads**

1. Navigate to your **Movies** or **TV Shows** section in Kodi.
2. Check that metadata (e.g., posters, synopses, cast information) is correctly displayed for each item.
3. If metadata is missing:
   - Right-click or long-press on the item and choose **Information**.
   - Manually select **Refresh** to download the metadata again.


This setup ensures that Kodi will read your media from the correct folders and display detailed metadata for movies and TV shows.

---

## **Tips for Maintaining and Enhancing Your Setup**


### **1. Updating Kodi's Library**

If a newly downloaded movie or TV show does not appear in Kodi:  
- This typically happens because the library needs to be updated to reflect the latest changes.

To manually update the library:  
1. Open Kodi and navigate to the **Movies** or **TV Shows** section.  
2. Click **Update Library** in the side menu, or long-press (right-click) on the source folder and select **Scan for new content**.  
3. Kodi will rescan the directory and add the new media along with its metadata.

For convenience:  
- Enabling **Update library on startup** (explained in the configuration section) ensures Kodi automatically checks for new media each time it launches.

---

### **2. Controlling Radarr and Sonarr from Your Mobile**

To manage Radarr and Sonarr directly from your phone, you can use the **Rudarr** app.  

- **Download Rudarr:**
  - [App Store (iOS)](https://apps.apple.com/es/app/ruddarr/id6476240130)    

---

#### **Setting Up Rudarr**

1. Open the Rudarr app after installation.
2. Add Radarr and Sonarr by providing the following details:
   - **URL**: Use the URL of your Radarr and Sonarr instances (e.g., `http://<your-local-ip>:7878` for Radarr, and `http://<your-local-ip>:8989` for Sonarr).
   - **API Key**: Found in Radarr and Sonarr under:
     - **Settings > General > Security**: Copy the API key.

Once configured, Rudarr allows you to search, add, and manage downloads directly from your mobile device, making it a powerful addition to your media automation setup.


