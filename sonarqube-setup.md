
# 🔍 SonarQube Setup Using Docker

This guide outlines how to run **SonarQube LTS Community Edition** in a Docker container on your local or remote Linux host.


## 🌐 Prerequisite: Docker Installation

To run SonarQube in a Docker container, Docker need to setup in system.

```bash 
# Copy docker-installation steps from docker-installation.md and Save in a file, for example, docker_installer.sh, and make it executable using:

chmod +x docker_installer.sh

# Then, you can run the script using:

./docker_installer.sh
````

This will install and setup docker in ubuntu.

---

## 🐳 Step 1: Run SonarQube Docker Container

Use the following command to pull and start the SonarQube container:

```bash
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
````

### Explanation:
This command will download the sonarqube:lts-community Docker image from Docker Hub if it's not already available locally.

* `-d`: Run container in **detached mode**.
* `--name sonar`: Assigns the name **sonar** to the container.
* `-p 9000:9000`: Maps **host port 9000** to **container port 9000**.
* `sonarqube:lts-community`: Specifies the image tag (Long-Term Support - Community Edition).

---
#### To install SonarQube using Docker with host directory mounts (for persistent data), follow these complete and production-friendly steps: (if you don't want to loose data on server shutdown/restart):

1. Create Host Directory for Persistent Data
    ```bash
    sudo mkdir -p /opt/sonarqube/data
    sudo mkdir -p /opt/sonarqube/extensions
    sudo mkdir -p /opt/sonarqube/logs

    sudo chown -R 999:999 /opt/sonarqube
    ```
    Permissions (optional but recommended):

2. Run SonarQube Container with Volume Mounts
    ```bash
    docker run -d \
    --name sonarqube \
    -p 9000:9000 \
    -v /opt/sonarqube/data:/opt/sonarqube/data \
    -v /opt/sonarqube/extensions:/opt/sonarqube/extensions \
    -v /opt/sonarqube/logs:/opt/sonarqube/logs \
    sonarqube:lts-community
    ```
    ### Explanation:
    
    * `-v /opt/sonarqube/data:/opt/sonarqube/data`: Persistent configuration and DB files
    * `-v /opt/sonarqube/extensions:/opt/sonarqube/extensions`: Plugins and language analyzers
    * `-v /opt/sonarqube/logs:/opt/sonarqube/logs`: Logs written to host

    ### 🔄 Optional Container Management
    #### Stop Sonarqube:
    ```bash
    docker stop sonarqube
    ```
    #### Start Sonarqube:
    ```bash
    docker start sonarqube
    ```
    #### Remove Sonarqube Container (data retained):
    ```bash
    docker rm sonarqube
    ```
    #### Data Backup tip:
    ```bash
    tar -czvf sonar_backup_$(date +%F).tar.gz /opt/sonarqube
    ```
---
## 🌐 Step 2: Access SonarQube UI

Open a web browser and navigate to:

```
http://<VM_PUBLIC_IP>:9000
```

* Replace `<VM_IP>` with your actual **host IP address** or **domain name**.
* Default login credentials:

  * **Username:** `admin`
  * **Password:** `admin`

---

This will start the SonarQube server, and you should be able to access it using the provided URL. If you're running Docker on a remote server or a different port, replace localhost with the appropriate hostname or IP address and adjust the port accordingly.