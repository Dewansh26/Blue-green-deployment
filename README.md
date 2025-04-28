# 🚀 Blue-Green Deployment for Flask Application

## 📚 Table of Contents

- [Project Overview](#-project-overview)
- [Project Structure](#-project-structure)
- [Prerequisites](#-prerequisites)
- [Install Docker Compose v2 on Debian (Official Way)](#-install-docker-compose-v2-on-debian-official-way)
- [Getting Started](#-getting-started)
- [Switching Traffic](#-switching-traffic)
- [Deployment Flow](#-deployment-flow)
- [Useful Docker Commands](#-useful-docker-commands)
- [Technologies Used](#-technologies-used)
- [Conclusion](#-conclusion)

---

## 🚀 Project Overview

This project demonstrates a **Blue-Green Deployment** strategy for a simple **Flask-based web application**, containerized using **Docker**, orchestrated with **Docker Compose**, and managed through **Nginx** as a reverse proxy.

The goal is to enable **zero-downtime deployments** by maintaining two identical production environments — **Blue** (live) and **Green** (staging) — and smoothly switching traffic between them.

---

## 📁 Project Structure

```
.
├── app
│   ├── templates
│   │   ├── home.html
│   │   ├── ip_lookup.html
│   │   └── dns_lookup.html
│   ├── app.py
│   ├── Dockerfile
│   └── requirements.txt
├── nginx
│   └── nginx_default.conf
├── docker-compose.blue.yml
├── docker-compose.green.yml
├── analytics.json
├── history.json
├── .gitignore
```

- **app/**: Flask application code and HTML templates.
- **nginx/**: Nginx reverse proxy configuration.
- **docker-compose.blue.yml**: Compose file for the Blue deployment.
- **docker-compose.green.yml**: Compose file for the Green deployment.
- **analytics.json/history.json**: (Optional) Data storage files.

---

## 🔗 Prerequisites

Ensure you have the following installed:

- [Docker](https://www.docker.com/)
- [Docker Compose](https://docs.docker.com/compose/)
- [Git](https://git-scm.com/)

---

## 🛠️ Install Docker Compose v2 on Debian (Official Way)

We'll add Docker’s official repository and install the `docker-compose-plugin`.

---

### ✅ Step 1: Install Required Dependencies

```bash
sudo apt update
sudo apt install ca-certificates curl gnupg lsb-release -y
```

---

### ✅ Step 2: Add Docker’s Official GPG Key

```bash
sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/debian/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

---

### ✅ Step 3: Set Up Docker’s Repository

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

---

### ✅ Step 4: Update Repos & Install Compose Plugin

```bash
sudo apt update
sudo apt install docker-compose-plugin -y
```

---

### ✅ Step 5: Verify Installation

```bash
docker compose version
```

You should see something like:

```
Docker Compose version v2.x.x
```

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/dewansh26/blue-green-deployment.git
cd blue-green-deployment
```

### 2. Build and Start the Blue Environment

```bash
docker-compose -f docker-compose.blue.yml up --build -d
```

The application will be accessible through the Nginx reverse proxy.

### 3. Deploy and Start the Green Environment

```bash
docker-compose -f docker-compose.green.yml up --build -d
```

This environment runs parallel but is **not live yet**.

---

## 🔀 Switching Traffic

To promote the Green environment to live production:

1. Update the **upstream** block in `nginx/nginx_default.conf`:

```nginx
upstream app {
    server green_app:5000; # Previously blue_app:5000
}
```

2. Reload the Nginx configuration:

```bash
docker exec <nginx_container_id_or_name> nginx -s reload
```

> **Tip**: You can find your Nginx container ID with `docker ps`.

---

### 🚨 Rollback

If something goes wrong, simply revert the `upstream` in Nginx back to:

```nginx
server blue_app:5000;
```
and reload Nginx.

---

## 📈 Deployment Flow

```plaintext
1. Blue Environment is Live.
2. Deploy new version to Green Environment.
3. Test Green Environment internally.
4. Update Nginx to route traffic to Green.
5. Shutdown Blue Environment (optional).
```

This minimizes downtime, provides a quick rollback plan, and ensures better production stability.

---

## 🔧 Useful Docker Commands

- **Stop all containers**:

```bash
docker-compose -f docker-compose.blue.yml down
docker-compose -f docker-compose.green.yml down
```

- **View logs**:

```bash
docker-compose -f docker-compose.blue.yml logs
docker-compose -f docker-compose.green.yml logs
```

- **List running containers**:

```bash
docker ps
```

- **Reload Nginx inside a container**:

```bash
docker exec <nginx_container_name> nginx -s reload
```

---

## 🛠️ Technologies Used

- **Python** (Flask)
- **Docker**
- **Docker Compose**
- **Nginx**
- **HTML5 **

---

## 🏁 Conclusion

This project provides a **simple, effective**, and **practical** example of implementing **Blue-Green Deployment** for Flask apps using Docker and Nginx. It ensures minimal downtime, fast rollbacks, and safer releases — all essential for real-world production environments.


---
