If you install **Trivy on the host** instead of inside the Jenkins container, you can access it from the Jenkins pipeline by ensuring the following:

---

### ✅ 1. **Install Trivy on Host**

On your host (EC2 or local machine):

```bash
sudo apt-get install wget gnupg -y

wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null

echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb generic main" | sudo tee -a /etc/apt/sources.list.d/trivy.list

sudo apt-get update
sudo apt-get install trivy -y
```

Test installation:

```bash
trivy --version
```

Reference : https://trivy.dev/latest/getting-started/installation/

---

### ✅ 2. **Ensure Trivy is Available Inside Jenkins Container**

Since Jenkins runs inside a Docker container, it does **not** share the host’s environment by default.

You have **two options**:

---

### **Option 1: Mount Host Trivy Binary into Jenkins Container**

Mount the host Trivy binary (`/usr/bin/trivy`) into the Jenkins container:

#### 🔁 Stop & remove current Jenkins container (if running):

```bash
docker stop jenkins
docker rm jenkins
```

#### 🚀 Start Jenkins with Trivy binary mount:

```bash
docker run -d \
  --name jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v /opt/jenkins_data:/var/jenkins_home \
  -v /usr/bin/trivy:/usr/bin/trivy \
  jenkins/jenkins:lts
```

> This mounts host's `/usr/bin/trivy` into the same path inside the Jenkins container.

Now from Jenkins, you can call Trivy directly:

```groovy
pipeline {
  agent any
  stages {
    stage('Trivy Scan') {
      steps {
        sh 'trivy --version'
        sh 'trivy fs --format table -o trivy-fs-report.html .'
      }
    }
  }
}
```

---

### **Option 2: Run Trivy Directly from Host Using SSH**

Instead of running Trivy inside Jenkins, execute it remotely from Jenkins via SSH to host.

Install SSH agent plugin in Jenkins and add host credentials, then run:

```groovy
pipeline {
  agent any
  stages {
    stage('Scan from Host') {
      steps {
        sshagent(['host-ssh-credentials-id']) {
          sh 'ssh user@host-ip "trivy fs /path/to/scan --format table -o /tmp/report.html"'
          sh 'scp user@host-ip:/tmp/report.html ./'
        }
      }
    }
  }
}
```

> This method requires Jenkins to be able to SSH into the host.

---

### ✅ Recommendation:

For simplicity and integration, **Option 1 (bind-mount host Trivy into container)** is the most direct and effective approach if you can control the Docker run command.

Let me know if your Jenkins is managed with `docker-compose` or Kubernetes, I can tailor accordingly.
