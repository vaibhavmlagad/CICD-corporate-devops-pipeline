# 🐳 Docker Installation on Ubuntu (via APT Repository)

This guide explains how to install **Docker Engine**, **Docker CLI**, **Buildx**, and **Docker Compose Plugin** using Docker’s official APT repository on Ubuntu.

Before you install Docker Engine for the first time on a new host machine, you need to set up the Docker apt repository. Afterward, you can install and update Docker from the repository.

---

## 🔐 Set up Docker's apt repository.

To install the latest version refer - https://docs.docker.com/engine/install/ubuntu/

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

---

## ⚙️ Install Docker packages

To install the latest version, run:

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Once, installation is completd, update the socket permissions
```bash
sudo chmod 666 /var/run/docker.sock
```

---

## ✅ Verify Docker Installation

Run the following command to validate the Docker installation:

```bash
sudo docker run hello-world
```

If successful, it will output a confirmation message from the Docker container runtime.

---

## 📎 Notes

* Docker daemon listens on Unix socket by default (`/var/run/docker.sock`).
* To manage Docker as a non-root user, you must add your user to the `docker` group:

  ```bash
  sudo usermod -aG docker $USER
  newgrp docker
  ```

---