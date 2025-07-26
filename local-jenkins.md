By default, **you will lose Jenkins data** (jobs, configs, plugins, etc.) if you run it in Docker **without a volume** and then stop or remove the container.

However, if you **mount a persistent volume** to the container, Jenkins data will **survive system reboots, Docker restarts, or container recreation**.

---

## ✅ Recommended: Use Docker Volume for Jenkins

Run Jenkins with a named or host-mounted volume:

```bash
docker run -d --name jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```

### What this does:

* `-v jenkins_home:/var/jenkins_home` creates a **named Docker volume** `jenkins_home`
* Jenkins stores all configuration, jobs, plugins, and secrets in `/var/jenkins_home`
* This data is persisted on the host and reused even if the container is restarted or removed

---

### 🔁 To stop and resume Jenkins safely:

```bash
docker stop jenkins
docker start jenkins
```

If you accidentally remove the container:

```bash
docker rm jenkins

# Recreate it using the same volume
docker run -d --name jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```

---

## 🧱 Alternative: Host Directory Mount (Optional)

If you want to **see and back up Jenkins data manually**, mount a host path:

```bash
docker run -d --name jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v /home/youruser/jenkins_data:/var/jenkins_home \
  jenkins/jenkins:lts
```

---

## 🔒 Permissions Note

Make sure the mounted directory is writable by the Jenkins user (`uid 1000` by default):

```bash
sudo chown -R 1000:1000 /home/youruser/jenkins_data
```

---

## ✅ Summary

| Setup Type      | Data Loss on Shutdown | Persistent | Recommendation |
| --------------- | --------------------- | ---------- | -------------- |
| No Volume       | ✅ Yes                 | ❌ No       | ❌ Avoid        |
| Named Volume    | ❌ No                  | ✅ Yes      | ✅ Preferred    |
| Host Path Mount | ❌ No                  | ✅ Yes      | ✅ Advanced     |

---

Let me know if you want to set up **backups**, **volume inspection**, or **migration to/from Kubernetes**.
