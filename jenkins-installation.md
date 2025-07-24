
# Jenkins Installation on Ubuntu (Debian-based)

This document provides step-by-step instructions to install **OpenJDK 21** and **Jenkins** on a Debian-based Linux system using the terminal.

---

## 📦 Java Installation (OpenJDK 21)

Jenkins requires Java to run. The following command installs **OpenJDK 21 (headless)**:

```bash
sudo apt update
sudo apt install openjdk-21-jre-headless -y
````

You can verify the Java installation using:

```bash
java -version
```

---

## ⚙️ Jenkins Installation

The following steps will install **Jenkins (LTS - stable release)** using the official Jenkins package repository:

```bash
# Download and add the Jenkins GPG key
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

# Add the Jenkins APT repository to the system
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | \
  sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# Update the APT package index
sudo apt-get update

# Install Jenkins
sudo apt-get install jenkins -y
```

After installation, start and enable Jenkins service:

```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

To check Jenkins status:

```bash
sudo systemctl status jenkins
```

Jenkins will be accessible at: `http://<your-server-ip>:8080`

---

## ✅ Notes

* REFERENCE : https://www.jenkins.io/doc/book/installing/linux/
* Ensure ports like **8080** are open in your firewall if accessing remotely.
* Default Jenkins initial admin password is located at:
  `/var/lib/jenkins/secrets/initialAdminPassword`

---
