# kodi-raspberrypi-mediacenter
Comprehensive guide to setting up a Raspberry Pi media center with LibreELEC and Kodi, using Docker for torrent automation with qBittorrent, Jackett, Sonarr, and Radarr. Learn how to configure external storage, organize your media library, and enable secure remote access for a seamless 4K home entertainment experience.

## Disclaimer

This project is intended for personal and educational purposes only. The author does not condone or support the use of this project for illegal activities, including downloading or accessing copyrighted content without proper authorization. Users are solely responsible for ensuring compliance with applicable laws in their jurisdiction.

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

## LibreELEC Filesystem Overview

LibreELEC uses a read-only root filesystem. The writable persistent storage is at `/storage` — this is where all configuration files, logs, and user data live. When this guide references paths like `/storage/external_disk/...`, it's because everything must live under `/storage` to survive reboots. Do not store configuration or media on the root filesystem (`/`) — changes there are lost on reboot.

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
       mkdir -p config/qbittorrent
       mkdir -p config/jackett
       mkdir -p config/sonarr
       mkdir -p config/radarr
       mkdir -p config/lidarr
       mkdir -p config/navidrome
       mkdir -p config/slskd
       mkdir -p docker/config/jellyfin
       mkdir -p docker/config/wg-easy
       mkdir -p downloads/movies-radarr
       mkdir -p downloads/tv-sonarr
       mkdir -p downloads/music-lidarr
       mkdir -p downloads/incomplete
       mkdir -p media/movies
       mkdir -p media/tv
       mkdir -p media/music
      ```

### 3. Explanation of the Directories  
   - **`config/qbittorrent`:** qBittorrent settings (web UI credentials, categories, download preferences).  
   - **`config/jackett`:** Jackett indexer configurations and API keys.  
   - **`config/sonarr`:** Sonarr settings (TV show library, download profiles, indexers).  
   - **`config/radarr`:** Radarr settings (movie library, download profiles, indexers).  
   - **`config/lidarr`:** Lidarr settings (music library, download profiles, indexers, plugins).  
   - **`config/navidrome`:** Navidrome music server settings and database.  
   - **`config/slskd`:** slskd configuration (YAML file and Soulseek credentials).  
   - **`docker/config/jellyfin`:** Jellyfin media server configuration and database.  
   - **`docker/config/wg-easy`:** WireGuard client profiles and server keys.  
   - **`downloads`:** Temporary storage for media files downloaded by the containers.  
       - **`movies-radarr`:** For movies downloaded by Radarr.  
       - **`tv-sonarr`:** For TV shows downloaded by Sonarr.  
       - **`music-lidarr`:** For music downloaded by Lidarr.  
       - **`incomplete`:** For in-progress downloads by slskd (Soulseek).  
   - **`media`:** Final destination for organized media files:  
       - **`movies`:** For properly imported and sorted movies.  
       - **`tv`:** For properly imported and sorted TV shows.  
       - **`music`:** For properly imported and sorted music (Artist/Album/Track).

### 4. Set Permissions for the Directories  
   - Ensure the Docker containers have full access by granting the appropriate permissions:
     ```
      chmod -R 777 docker downloads media
      chmod g+s downloads
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
- **Lidarr**: For managing and automating music downloads.  
- **Navidrome**: A lightweight music streaming server for accessing your library from any device.  
- **Jellyfin**: An open-source media server for streaming your library to any device.  
- **slskd**: A headless Soulseek client for peer-to-peer music discovery and downloading.  
- **Tubifarry**: A Lidarr plugin that adds Soulseek as a native indexer and download client, so Lidarr searches and downloads from Soulseek directly — no external bridge needed.  

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
#### **Lidarr**  
Lidarr is the music equivalent of Sonarr and Radarr — it automates finding, downloading, and organizing music. The **nightly** tag is required for the plugin system (Tubifarry).  

```bash
docker run -d \
  --name lidarr \
  --network media-network \
  -p 8686:8686 \
  -v /storage/external_disk/config/lidarr:/config \
  -v /storage/external_disk/media/music:/music \
  -v /storage/external_disk/downloads:/downloads \
  -e PUID=1000 -e PGID=1000 \
  --restart unless-stopped \
  linuxserver/lidarr:nightly
```
#### **Navidrome**  
Navidrome is a lightweight, modern music streaming server compatible with the Subsonic API — think of it as your personal Spotify. It serves your music library to web browsers and mobile apps.  

```bash
docker run -d \
  --name navidrome \
  --network media-network \
  -p 4533:4533 \
  -v /storage/external_disk/config/navidrome:/data \
  -v /storage/external_disk/media/music:/music:ro \
  -e ND_MUSICFOLDER=/music \
  -e PUID=1000 -e PGID=1000 \
  --restart unless-stopped \
  deluan/navidrome
```
#### Navidrome Initial Setup

1. Open your browser and navigate to `http://<your-pi-ip>:4533`.
2. The first time you visit, you'll see a setup screen. Create the admin account by setting a username and password.
3. After login, Navidrome automatically scans `/music` and begins indexing your library. Album art, artist images, and metadata are fetched automatically from MusicBrainz and Last.fm.

#### **Jellyfin**
Jellyfin is an open-source media server that streams your movies, TV shows, and music to any device — phones, tablets, laptops, or smart TVs — with optimized playback and hardware transcoding.

```bash
docker run -d \
  --name jellyfin \
  --network media-network \
  --device /dev/dri:/dev/dri \
  -p 8096:8096 \
  -v /storage/external_disk/docker/config/jellyfin:/config \
  -v /storage/external_disk/media/tv:/data/tvshows \
  -v /storage/external_disk/media/movies:/data/movies \
  -v /storage/external_disk/media/music:/data/music \
  -e PUID=1000 -e PGID=1000 \
  --restart unless-stopped \
  linuxserver/jellyfin
```

- `--device /dev/dri:/dev/dri` passes the Raspberry Pi 5's hardware video decoder through to the container, enabling hardware-accelerated transcoding.
- Media folders are mounted under `/data` to follow Jellyfin's recommended path structure.

#### **slskd**  
slskd is a headless Soulseek client — it connects to the Soulseek peer-to-peer network where a massive catalog of music is shared by millions of users. It serves as an alternative download source to torrents for Lidarr, especially useful for rare or obscure music.

Create `/storage/external_disk/config/slskd/slskd.yml` with the following content:

```yaml
directories:
  downloads: /downloads/music-lidarr
  incomplete: /downloads/incomplete
  # shared:           # UNCOMMENT ONLY IF you want other Soulseek users to
  #   - /music        # download files from your library. Review what you own.
soulseek:
  username: <your-soulseek-username>
  password: <your-soulseek-password>
web:
  port: 5030
  authentication:
    disabled: false
    username: admin
    password: <choose-a-password>
    api_keys:
      tubifarry:
        key: <generate-with-openssl-rand-hex-16>
        role: ReadWrite
```

> Register a free account on the Soulseek network first — you can use any Soulseek desktop client or [register via Nicotine+](https://nicotine-plus.org/doc/SOULSEEK.htm).

Now run the container:

```bash
docker run -d \
  --name slskd \
  --network media-network \
  -p 5030:5030 \
  -p 5031:5031 \
  -v /storage/external_disk/config/slskd:/app \
  -v /storage/external_disk/media/music:/music:ro \
  -v /storage/external_disk/downloads:/downloads \
  -e PUID=1000 -e PGID=1000 \
  -e SLSKD_UMASK=002 \
  --restart unless-stopped \
  slskd/slskd
```

- Port 5030: Web UI (check status, browse network, search)
- Port 5031: Soulseek protocol (needed for peer connections — also forward this UDP port on your router for better connectivity)
- The `shared` directory in the config lets other Soulseek users browse and download files from your library. This is disabled by default in the YAML above. Enable it only if you intend to participate as a sharer on the network and have reviewed which folders are exposed.

#### slskd Initial Setup

1. Open your browser and navigate to `http://<your-pi-ip>:5030`.
2. You'll see the slskd dashboard showing connection status, active transfers, and search capabilities.
3. Check the status indicator at the top. It should show **Connected** in green once your Soulseek credentials are accepted.
4. If it shows **Disconnected**, verify the username and password in your `slskd.yml` file and restart the container: `docker restart slskd`.
5. Generate a random API key for Tubifarry by running `openssl rand -hex 16` on your Pi, then copy the output into `slskd.yml` under `web.authentication.api_keys.tubifarry.key` and restart slskd.

#### **Tubifarry (Lidarr Plugin)**  
Tubifarry is a Lidarr plugin that adds Soulseek as a **native indexer and download client** directly inside Lidarr. Unlike Soularr (which ran as a separate polling container), Tubifarry integrates Soulseek the same way Jackett + qBittorrent work for torrents — when you search for an artist in Lidarr, it queries Soulseek alongside your torrent indexers, and you can pick releases from either source. No extra Docker container, config file, or cron-like polling needed.

##### Install the Plugin

1. Open Lidarr at `http://<your-pi-ip>:8686`.
2. Go to **System > Plugins**.
3. Paste `https://github.com/TypNull/Tubifarry` into the GitHub URL box and click **Install**.

##### Configure the Soulseek Indexer

1. In Lidarr, go to **Settings > Indexers** and click **Add** (**+**).
2. Select **Slskd** from the list (added by Tubifarry).
3. Configure:
   - **URL:** `http://slskd:5030`
   - **API Key:** The key you generated and placed in `slskd.yml` under `web.authentication.api_keys.tubifarry.key`
   - **Include Only Audio Files:** Enable
4. Click **Test** to verify the connection, then **Save**.

##### Configure the Soulseek Download Client

1. In Lidarr, go to **Settings > Download Clients** and click **Add** (**+**).
2. Select **Slskd** from the list (added by Tubifarry).
3. The download path is fetched automatically from slskd. If paths don't match between containers, use **Remote Path Mappings** in Lidarr settings.
4. Click **Test** to verify, then **Save**.

Lidarr now has Soulseek as a fully integrated download source — search results from Soulseek appear alongside torrent results, and downloads are managed directly by Lidarr.

> **Tip:** Tubifarry includes optional features like YouTube downloads, Spotify playlist imports, and soundtrack fetching from Sonarr/Radarr. See the [Tubifarry docs](https://github.com/TypNull/Tubifarry) if you want to explore them later.

### 3. Verify the Containers
   Run the following command to ensure all containers are up and running:  
   ```bash
   docker ps
   ```
---
## Configuring initial Setup

### Sonarr

#### Step 1: Access Sonarr
1. Open your browser and navigate to the IP address of your Raspberry Pi with the Sonarr port (default: `http://<your-pi-ip>:8989`).
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
1. Open your browser and navigate to the IP address of your Raspberry Pi with the Radarr port (default: `http://<your-pi-ip>:7878`).
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

### Lidarr

#### Step 1: Access Lidarr
1. Open your browser and navigate to the IP address of your Raspberry Pi with the Lidarr port (default: `http://<your-pi-ip>:8686`).
   Example: `http://192.168.1.100:8686`.

2. You'll see the Lidarr dashboard. If it's your first time accessing it, follow the setup wizard or proceed with the manual setup below.

#### Step 2: Set Up Authentication
1. Navigate to **Settings** > **General**.
2. In the **Security** section:
   - Set a **Username** and **Password** for accessing Lidarr.
   - Enable the option **Bypass Authentication for Local Addresses** to allow access without authentication from localhost.
   - Click **Save** to apply the changes.

#### Step 3: Configure Root Folder
1. Go to **Settings** > **Media Management**.
2. In the **Root Folders** section:
   - Click **Add Root Folder**.
   - Set the root folder to `/music` (this is the path where your music will be stored).
   - Save the configuration.

#### Step 4: Configure Download Clients and Indexers

Lidarr uses two download sources in parallel:

- **Torrents (Jackett + qBittorrent):** Follow the shared instructions in [Connecting Sonarr, Radarr, and Lidarr with qBittorrent and Jackett](#connecting-sonarr-radarr-and-lidarr-with-qbittorrent-and-jackett) below. For Tornab indexers, use category `3000,3010,3020`.
- **Soulseek (Tubifarry + slskd):** Already configured in the [Tubifarry](#tubifarry-lidarr-plugin) section above.

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
1. Open your browser and navigate to the qBittorrent web UI: `http://<your-pi-ip>:8080`. Example: `http://192.168.1.100:8080`.
2. Log in using:
   - Username: admin
   - Password: The default password retrieved in Step 1.

#### Step 3: Change the Default Password
1. Once logged in, go to **Tools** > **Options** > **Web UI**.
2. Under the Authentication section:
   - Enter a new password in the Password field and confirm it in the Confirm Password field.
   - Click Apply to save the new password.

#### Step 4: Create Categories for Radarr, Sonarr, and Lidarr
1. Go to the **Categories** tab in qBittorrent.
2. Create the following categories (the category save path overrides the default for any torrent assigned to that category):
   - **movies-radarr**:
     - Set the save path to `/downloads/movies-radarr`.
   - **tv-sonarr**:
     - Set the save path to `/downloads/tv-sonarr`.
   - **music-lidarr**:
     - Set the save path to `/downloads/music-lidarr`.
3. Click **Apply** to save the categories.

#### Step 5: Set the Default Download Folder (fallback)
1. Go to the **Downloads** tab.
2. Under **Default Save Path**, set the path to `/downloads`.
3. This path is only used for torrents that have no assigned category — all *arr downloads use the category paths set above. Click **Apply**.

---

### Jackett

#### Step 1: Access the Jackett Web UI
1. Open your browser and navigate to the Jackett web UI:
   `http://<your-pi-ip>:9117`.
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
2. Your Jackett configuration is now complete, and it's ready to be linked with Sonarr, Radarr, and Lidarr.

---

### Tubifarry Verification

Tubifarry runs inside Lidarr as a plugin, so there is no separate container to check. To confirm everything is working:

1. Open Lidarr and go to **System > Plugins** — Tubifarry should appear in the installed plugins list.
2. Go to **Settings > Indexers** — click **Test** on the Slskd indexer you configured. It should return a green checkmark.
3. Go to **Settings > Download Clients** — click **Test** on the Slskd download client. It should also return a green checkmark.

If either test fails, verify the slskd container is running (`docker ps | grep slskd`), the API key in `slskd.yml` matches what you entered in Lidarr, and slskd was restarted after editing the config.

This completes the initial setup.

---

## Connecting Sonarr, Radarr, and Lidarr with qBittorrent and Jackett

In this section, we will configure Sonarr, Radarr, and Lidarr to connect with qBittorrent for downloading content and with Jackett for accessing indexers.


### **1. Adding qBittorrent to Sonarr, Radarr, and Lidarr**

1. **Open Sonarr, Radarr, or Lidarr** in your browser.
   - Sonarr: `http://<your-pi-ip>:8989`
   - Radarr: `http://<your-pi-ip>:7878`
   - Lidarr: `http://<your-pi-ip>:8686`

2. **Navigate to the Download Client settings:**
   - In Sonarr, Radarr, or Lidarr, go to **Settings > Download Clients**.
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

### **2. Adding Jackett Indexers to Sonarr, Radarr, and Lidarr**

#### **Step 1: Get the Indexer URL and API Key from Jackett**
1. **Access Jackett:**
   - Open Jackett in your browser: `http://<your-pi-ip>:9117`.

2. **Copy the Torznab Feed URL:**
   - Find an indexer you have added in Jackett and click **Copy Torznab Feed**.
   - The copied URL will look like this:
     ```
     http://<your-pi-ip>:9117/api/v2.0/indexers/<indexer_name>/results/torznab/
     ```
   - Replace `<your-pi-ip>` with `jackett` to take advantage of the Docker network:
     ```
     http://jackett:9117/api/v2.0/indexers/<indexer_name>/results/torznab/
     ```

3. **Get the API Key:**
   - In Jackett, go to **API Key** in the top-right corner of the interface.
   - Copy the key for use in Sonarr, Radarr, and Lidarr.

#### **Step 2: Add the Indexer to Sonarr, Radarr, and Lidarr**
1. **Open Sonarr, Radarr, or Lidarr** and go to **Settings > Indexers**.
2. **Add a New Indexer:**
   - Click **+** and select **Torznab**.
3. **Configure the Torznab Indexer:**
   - **Name:** Enter a friendly name for the indexer.
   - **URL:** Paste the Torznab Feed URL (modified to use `jackett` instead of the IP address).
   - **API Key:** Paste the API Key copied from Jackett.
   - **Categories:**
     - For **Sonarr**, use the category: `5000, 5070`.
     - For **Radarr**, use the category: `2000, 2010`.
     - For **Lidarr**, use the category: `3000, 3010, 3020`.
   - Click **Test** to ensure the connection works.
   - Click **Save**.

---

### **3. Testing the Setup**

1. **Search for Titles:**
   - In Sonarr, Radarr, or Lidarr, go to **Add New Series**, **Add New Movie**, or **Add New Artist**.
   - Search for a title and add it to your library.

2. **Verify Download Client Integration:**
   - When a title is searched, Sonarr, Radarr, or Lidarr will use the Jackett indexer to find torrents and send them to qBittorrent.

3. **Check qBittorrent:**
   - Open qBittorrent and verify that the download appears under the correct category:
     - **TV-Sonarr**: Downloads TV series.
     - **Movies-Radarr**: Downloads movies.
      - **Music-Lidarr**: Downloads music.

---

### **4. How Lidarr Works with Both Torrents and Soulseek**

Lidarr can use **both** download sources simultaneously — there's no need to pick one:

- **Torrent path (Jackett + qBittorrent):** Set up in the steps above using Torznab indexers and the qBittorrent download client. Useful for popular releases that appear on torrent trackers.
- **Soulseek path (Tubifarry + slskd):** Tubifarry adds Soulseek as a native indexer and download client within Lidarr. When you search for a release, Soulseek results appear alongside torrent results in the same interface. Downloads are sent to slskd and handled by Lidarr just like any other download client.

When you add an artist in Lidarr:

1. Lidarr searches all configured indexers — Jackett (torrents) and Slskd (Soulseek) — in parallel.
2. Search results from both sources appear together under the artist's search tab.
3. You pick a release, Lidarr sends it to the matching download client (qBittorrent for torrents, slskd for Soulseek).
4. Lidarr monitors the download and imports it to `/music` once complete.

Lidarr automatically detects newly downloaded music in the root folder (`/music`) and imports it, regardless of which source provided the files.

> **Tip:** If you find that torrents are sufficient for your music tastes, you can remove the slskd container at any time and disable the Slskd indexer/download client in Lidarr without affecting anything else. The Jackett/qBittorrent integration is fully independent.

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

#### **5. Set Up the Music Section**

Kodi can also manage and play your music library with album art, artist biographies, and other metadata.

1. Go to **Settings (Gear Icon) > Media Settings > Library**.
2. Select **Music**, then click **Add Music...**.
3. In the dialog box that appears:
   - **Browse** to the folder where your music is stored: `/media/music`.
   - Click **OK** to confirm.
4. Enter a name for this source, e.g., `Music`, and click **OK**.
5. In the next screen:
   - Choose a scraper for metadata (e.g., **Universal Album Scraper** or **Universal Artist Scraper**).
   - Click **OK**.
6. In the confirmation prompt, select **Yes** to scan the folder and download album art and artist information.

Your music will now appear in Kodi's **Music** section with full metadata, and the automatic library updates (enabled in step 3) will keep it current when new albums are added.

---

## Setting Up Jellyfin for Multi-Device Streaming

Jellyfin is an open-source media server that streams your movies and TV shows to any device — phones, tablets, laptops, or smart TVs — with optimized playback and hardware transcoding. It complements Kodi: use Kodi for viewing directly on your TV, and Jellyfin's apps or web UI when watching on other devices.

### 1. Initial Setup

1. Open your browser and navigate to `http://<your-pi-ip>:8096` (e.g., `http://192.168.1.100:8096`).
2. The setup wizard appears. Choose your preferred language and click **Next**.
3. Create an admin account by setting a username and password.
4. Click **Add Media Library**, then configure your libraries:

   **Movies library:**
   - **Content type:** Movies
   - **Display name:** Movies
   - **Folders:** Click the **+** button, enter `/data/movies`, and confirm.

    **TV Shows library:**
    - Click **Add Media Library** again.
    - **Content type:** Shows
    - **Display name:** TV Shows
    - **Folders:** Click **+**, enter `/data/tvshows`, and confirm.

    **Music library:**
    - Click **Add Media Library** again.
    - **Content type:** Music
    - **Display name:** Music
    - **Folders:** Click **+**, enter `/data/music`, and confirm.

5. Click **Next**, then **Finish** to complete the wizard. Jellyfin will begin scanning your media folders and downloading metadata.

### 2. Enable Hardware Acceleration

Hardware acceleration offloads video transcoding to the Raspberry Pi 5's GPU, reducing CPU usage and improving streaming performance — especially important for 4K content or playback on devices that don't natively support your media formats.

1. In Jellyfin, go to **Dashboard** (the gear icon in the top-right) → **Playback**.
2. Under **Hardware Acceleration**, select **Video4Linux2 (V4L2)** from the dropdown.
3. Check **Enable hardware encoding**.
4. Check the following codec boxes under "Enable hardware decoding for":
   - **H264**
   - **HEVC**
   - **MPEG2**
   - **VC1**
   - **VP8**
   - **VP9**
5. Scroll down and click **Save** at the bottom of the page.

### 3. Access Jellyfin

| Device | Method |
|---|---|
| **Web browser** | `http://<your-pi-ip>:8096` |
| **iPhone / iPad** | Jellyfin app from the [App Store](https://apps.apple.com/app/jellyfin/id1480192618) |
| **Android** | Jellyfin app from the [Play Store](https://play.google.com/store/apps/details?id=org.jellyfin.mobile) |
| **Smart TV** | Jellyfin app available on most platforms (LG, Samsung, Android TV, Roku) |
| **Via WireGuard VPN** | `http://10.8.0.1:8096` — stream your media from anywhere |

### 4. Optional: Sync Kodi with Jellyfin

Install the **Jellyfin for Kodi** add-on to keep watched status and library updates synchronized between Kodi and Jellyfin:

1. Install the [Jellyfin Kodi repository](https://jellyfin.org/docs/general/clients/kodi/#install) from the Kodi file manager.
2. Open Kodi, go to **Add-ons** → **Install from repository** → **Jellyfin Repository** → **Video add-ons** → **Jellyfin**.
3. Follow the prompt to connect to your Jellyfin server at `http://<your-pi-ip>:8096`.
4. Choose **Native mode** during setup for the best Kodi integration.

Now when you watch something in Kodi, Jellyfin marks it as watched — and vice versa.

---

## Setting Up Navidrome for Music Streaming

Navidrome is a lightweight, self-hosted music server compatible with the Subsonic API, giving you a personal Spotify-like experience. It streams your music library to web browsers and mobile apps with gapless playback, transcoding, and smart playlists. The container was deployed in the Docker Containers section above.

### 1. Connect Mobile Apps and Desktop Clients

Navidrome is compatible with any **Subsonic client**. Here are some popular options:

| Platform | Recommended App |
|---|---|
| **iPhone / iPad** | [play:Sub](https://apps.apple.com/app/playsub-music-streamer/id995329482) or [Substreamer](https://apps.apple.com/app/substreamer/id1012991665) |
| **Android** | [Symfonium](https://play.google.com/store/apps/details?id=app.symfonik.music.player) (paid) or [Subtracks](https://play.google.com/store/apps/details?id=com.subtrack) (free) |
| **Desktop (Web)** | `http://<your-pi-ip>:4533` |
| **Desktop (App)** | [Sonixd](https://github.com/jeffvli/sonixd) or [Feishin](https://github.com/jeffvli/feishin) |

To connect a Subsonic client:
- **Server:** `http://<your-pi-ip>:4533`
- **Username / Password:** The admin credentials you created in step 1.

### 2. Access via WireGuard VPN

When connected to your WireGuard VPN, stream your music from anywhere:
- **Web:** `http://10.8.0.1:4533`
- **Mobile apps:** Use `http://10.8.0.1:4533` as the server address.

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

### 2. Updating Docker Containers

Container images don't update themselves. To pull the latest image and recreate a container:

```bash
docker pull linuxserver/qbittorrent && docker stop qbittorrent && docker rm qbittorrent && docker run -d \
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

Repeat the same pattern for each container (jackett, sonarr, radarr, lidarr, navidrome, slskd, jellyfin, wireguard, duckdns) — always `pull`, `stop`, `rm`, then `run` with the same flags from the deployment section. Your configuration volumes are preserved because the `-v` mounts point to the same directories.

> **Tip:** Run `docker system prune -a` every few months to reclaim disk space from old unused images.

---

### **3. Controlling Radarr and Sonarr from Your Mobile**

To manage Radarr and Sonarr directly from your phone, you can use the **Rudarr** app.  

- **Download Rudarr:**
  - [App Store (iOS)](https://apps.apple.com/es/app/ruddarr/id6476240130)    


#### **Setting Up Rudarr**

1. Open the Rudarr app after installation.
2. Add Radarr and Sonarr by providing the following details:
   - **URL**: Use the URL of your Radarr and Sonarr instances (e.g., `http://<your-pi-ip>:7878` for Radarr, and `http://<your-pi-ip>:8989` for Sonarr).
   - **API Key**: Found in Radarr and Sonarr under:
     - **Settings > General > Security**: Copy the API key.

Once configured, Rudarr allows you to search, add, and manage downloads directly from your mobile device, making it a powerful addition to your media automation setup.

---

### 4. Monitor Disk Space

Run `df -h /storage/external_disk` periodically to check remaining space on your external drive. Docker container logs can grow large — check with `docker system df`. If `/storage` fills up, containers will fail to start.

---

## Backing Up Your Configuration

All container configuration lives in `/storage/external_disk/config/` and `/storage/external_disk/docker/config/`. Back these up regularly to avoid losing your setup.

### Quick Backup to a USB Stick

1. Insert a second USB drive. It will appear as `/dev/sdb1` (check with `blkid`).
2. Mount it and copy:

```bash
mkdir -p /tmp/backup-mount
mount /dev/sdb1 /tmp/backup-mount
cp -r /storage/external_disk/config /tmp/backup-mount/
cp -r /storage/external_disk/docker/config /tmp/backup-mount/
umount /tmp/backup-mount
```

### What to Back Up

| Directory | Contains |
|---|---|
| `config/qbittorrent` | qBittorrent settings, categories |
| `config/jackett` | Jackett indexers, API key |
| `config/sonarr` | Sonarr library, profiles, API key |
| `config/radarr` | Radarr library, profiles, API key |
| `config/lidarr` | Lidarr library, profiles, plugins, API key |
| `config/slskd` | slskd.yml, Soulseek credentials |
| `docker/config/wg-easy` | WireGuard server keys, client profiles |

These are small (usually < 100 MB total). Your media files in `/storage/external_disk/media/` are large and should be backed up separately if you can't re-download them.

---

## Secure Remote Access with WireGuard and DuckDNS

This section explains how to access your media center services (Sonarr, Radarr, qBittorrent, Jackett, and Kodi) from outside your home network through an encrypted WireGuard VPN tunnel. DuckDNS provides a stable domain name so your devices can always find your Raspberry Pi, even when your home IP address changes.

### 1. Register a DuckDNS Domain

1. Go to [https://www.duckdns.org](https://www.duckdns.org) and sign in with your GitHub or Google account.
2. Create a subdomain of your choice (e.g., `myhome` — this gives you `myhome.duckdns.org`).
3. Copy the **token** string displayed at the top of the page. You will need it in the next step.

### 2. Deploy the DuckDNS Container

The DuckDNS container periodically reports your home IP to DuckDNS so your domain always resolves correctly. It requires no config volume — all settings are passed as environment variables.

```bash
docker run -d \
  --name duckdns \
  --network media-network \
  -e SUBDOMAINS=yourdomain \
  -e TOKEN=your-token-uuid \
  -e LOG_FILE=false \
  --restart unless-stopped \
  linuxserver/duckdns
```

- Replace `yourdomain` with the subdomain you created (e.g., `myhome`).
- Replace `your-token-uuid` with the token copied from DuckDNS.

Verify it is running and working:

```bash
docker logs duckdns
```

You should see a log line ending with `OK`. If you see `KO`, double-check your subdomain and token.

### 3. Set Up Port Forwarding on Your Router

WireGuard uses the UDP protocol on port **51820**. You must configure your router to forward incoming UDP traffic on this port to your Raspberry Pi's local IP address.

Every router interface is different, but the general steps are:

1. Log in to your router's admin panel (typically `http://192.168.1.1`).
2. Find the **Port Forwarding** or **NAT** section.
3. Add a new rule with these settings:
   - **Protocol:** UDP
   - **External port:** 51820
   - **Internal IP:** Your Raspberry Pi's local IP (e.g., `192.168.1.100`)
   - **Internal port:** 51820
4. Save and apply the rule.

If you need model-specific instructions, [https://portforward.com](https://portforward.com) is a good resource.

### 4. Configure WireGuard with wg-easy

`wg-easy` is a Docker image that runs WireGuard inside a container with a simple web interface for managing clients. It handles key generation, firewall rules, and QR code display automatically — no manual configuration files needed.

#### 4.1 Enable Kernel Settings

WireGuard needs two kernel parameters enabled on the host. Run these commands once:

```bash
sysctl -w net.ipv4.ip_forward=1
sysctl -w net.ipv4.conf.all.src_valid_mark=1
```

To make these survive a reboot, add them to LibreELEC's autostart script:

```bash
nano /storage/.config/autostart.sh
```

Append the following lines (if the file is empty, add them as the first lines):

```bash
# Enable IP forwarding for WireGuard
sysctl -w net.ipv4.ip_forward=1
sysctl -w net.ipv4.conf.all.src_valid_mark=1
```

Save with `Ctrl+O`, exit with `Ctrl+X`.

#### 4.2 Run the wg-easy Container

Now run the container:

```bash
docker run -d \
  --name wireguard \
  --cap-add NET_ADMIN \
  --cap-add SYS_MODULE \
  --network host \
  -v /storage/external_disk/docker/config/wg-easy:/etc/wireguard \
  -e WG_HOST=yourdomain.duckdns.org \
  -e WG_PORT=51820 \
  -e WG_DEFAULT_DNS=1.1.1.1 \
  --restart unless-stopped \
  ghcr.io/wg-easy/wg-easy
```

- Replace `yourdomain.duckdns.org` with your full DuckDNS domain.
- `WG_DEFAULT_DNS=1.1.1.1` sets Cloudflare DNS for connected clients. Change this to your preferred DNS server if desired.

Verify the container started successfully:

```bash
docker logs wireguard
```

You should see lines confirming the server is listening and the WireGuard configuration was loaded and synced.

#### 4.3 Access the Web UI

Open your browser and navigate to the wg-easy web interface:

```
http://<your-pi-ip>:51821
```

For example: `http://192.168.1.100:51821`

The web interface shows your current WireGuard clients and provides buttons to add new ones, display QR codes, download configuration files, and enable or disable individual clients.

#### 4.4 Generate Your First Client

1. In the web UI, click the **New Client** button (the **+** icon).
2. Enter a name for the client (e.g., `iPhone`).
3. Click **Create**.
4. A new row appears with the client name and a QR code icon. Click the QR code icon to display the scannable code.

You can also click the download icon to save the `.conf` file as a backup.

### 5. Configure Your Client Device (iPhone / iPad)

1. Install the official **WireGuard** app from the App Store.
2. Open the app, tap the **+** button, and select **Create from QR code**.
3. Point your camera at the QR code displayed in the wg-easy web UI.
4. Give the tunnel a name (e.g., `Home`) and tap **Save**.

> **About Allowed IPs:** By default, wg-easy generates client configurations that route all internet traffic through your home connection (`AllowedIPs = 0.0.0.0/0`). If you prefer a *split tunnel* where only traffic to your media center goes through the VPN (leaving web browsing and streaming on your phone's direct connection), tap the tunnel in the WireGuard app, select **Edit**, and change **Allowed IPs** to `10.8.0.0/24`. Either setting works — pick whichever you prefer.

### 6. Verify the Connection

1. **On your phone:** Turn off Wi-Fi so you are on cellular data only.
2. Open the WireGuard app and toggle the tunnel **ON**.
3. Open Safari and navigate to one of your services using the Pi's VPN address (`10.8.0.1`):
   - Sonarr: `http://10.8.0.1:8989`
   - Radarr: `http://10.8.0.1:7878`
   - Lidarr: `http://10.8.0.1:8686`
   - qBittorrent: `http://10.8.0.1:8080`
   - Jellyfin: `http://10.8.0.1:8096`
   - Navidrome: `http://10.8.0.1:4533`
   - slskd: `http://10.8.0.1:5030`

> **Why `10.8.0.1`?** wg-easy assigns `10.8.0.1` to the WireGuard server (your Raspberry Pi) by default. Docker containers map their ports to all host interfaces, so services are reachable at this VPN address when connected. You can verify the address by running `docker exec wireguard cat /etc/wireguard/wg0.conf` on the Pi.

If the pages load, your WireGuard tunnel is working correctly. You can now manage downloads, add movies and TV shows, and check your media center from anywhere.

> **Troubleshooting:** If pages do not load, verify the container is running with `docker ps`. Check the logs with `docker logs wireguard` for any errors. Confirm your router's UDP port 51820 forwarding rule is active and pointing to the correct Pi IP address.

### 7. Adding Additional Clients

To add another device (an iPad, laptop, or a second phone):

1. Open the wg-easy web UI at `http://<your-pi-ip>:51821`.
2. Click **New Client**, give it a name (e.g., `iPad`), and click **Create**.
3. Scan the QR code from the web UI with the new device's WireGuard app.

Existing clients continue to work without interruption — each new client is added alongside the existing ones automatically.

## Troubleshooting

### Container Fails to Start

```bash
docker logs <container-name>  # Check for error messages
docker inspect <container-name>  # Check mount points and network
```

Common causes:
- **Port already in use:** Another service is using the mapped port. Check with `netstat -tlnp | grep <port>`.
- **Wrong config path:** A typo in a `-v` mount path. Verify the directory exists with `ls`.
- **Permission denied:** The container's PUID/PGID does not have access to the mounted directory. Run `chmod -R 777 /storage/external_disk/config/<service>` to fix.

### Services Not Reachable

- **Wrong IP:** Your Pi's IP may have changed. Check with `ip addr show eth0 | grep inet`.
- **Docker network issue:** Ensure the container is on `media-network` with `docker network inspect media-network`.
- **LibreELEC firewall:** LibreELEC has no firewall by default, so this is rarely the cause.

### Media Not Appearing in Kodi

- **Library needs refresh:** Go to the Movies/TV Shows section, open the side menu, click **Update Library**.
- **Wrong folder:** Verify Kodi is pointing to `/media/movies` and `/media/tv`, not the `/downloads` folders.
- **Naming:** Kodi scrapers need properly named files. Sonarr/Radarr handle this automatically, but manual downloads may need renaming.

### WireGuard Won't Connect

- **Port forwarding:** Confirm your router is forwarding UDP 51820 to your Pi's local IP.
- **DuckDNS not updating:** Check `docker logs duckdns` — you should see `OK`. If `KO`, verify your token and subdomain.
- **Carrier-grade NAT:** Some ISPs use CGNAT — WireGuard won't work behind it. Contact your ISP or request a static IP.

