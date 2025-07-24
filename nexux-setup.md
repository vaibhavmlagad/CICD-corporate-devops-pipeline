# 📦 Nexus Repository Manager 3 Setup Using Docker

This guide provides steps to deploy **Sonatype Nexus 3** in a Docker container and retrieve the initial admin password.

## 🌐 Prerequisite: Docker Installation

To run Nexus3 in a Docker container, Docker need to setup in system.

```bash 
# Copy docker-installation steps from docker-installation.md and Save in a file, for example, docker_installer.sh, and make it executable using:

chmod +x docker_installer.sh

# Then, you can run the script using:

./docker_installer.sh
````

This will install and setup docker in ubuntu.

---

## 🐳 Step 1: Run Nexus Container

To create a Docker container running Nexus 3 and exposing it on port 8081, you can use the following command:

```bash
docker run -d --name nexus -p 8081:8081 sonatype/nexus3:latest
````

### Explanation:

* `-d`: Run the container in detached mode.
* `--name nexus`: Name the container `nexus`.
* `-p 8081:8081`: Map **host port 8081** to **container port 8081**.
* `sonatype/nexus3:latest`: Use the latest Nexus 3 image from Sonatype repository.

---

## 🌐 Step 2: Access Nexus UI

Once the container is running, access the Nexus web interface via:

```
http://<HOST_PUBLIC_IP>:8081
```

Replace `<HOST_IP>` with your server or VM's IP address or hostname.

---

## 🔐 Step 3: Retrieve Initial Admin Password

To log in to the Nexus UI, you'll need the initial admin password, which is stored in the container.

### Step-by-step:

1. **Get the Container ID:**

   ```bash
   docker ps
   ```

2. **Access the Nexus container shell:**

   ```bash
   docker exec -it <container_id> /bin/bash
   ```

3. **Navigate to the password file:**

   ```bash
   cd sonatype-work/nexus3
   ```

4. **Display the admin password:**

   ```bash
   cat admin.password
   ```

5. **Exit the container:**

   ```bash
   exit
   ```

---

## 🔑 Default Login

* **Username:** `admin`
* **Password:** *(from `admin.password` file)*

You will be prompted to change the password after first login.

---

This process allows you to access the Nexus admin password stored within the container. Make sure to keep this password secure, as it grants administrative access to your Nexus instance.