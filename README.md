# 📈 Monitor Dockerized Microservice with Prometheus & Grafana via Ansible

## 📝 Project Overview

This project demonstrates how to monitor a Dockerized microservice application using **Prometheus**, **Node Exporter**, **Grafana**, and **cAdvisor** — all provisioned and configured using **Ansible**.

### 🔍 Key Objectives

- 🔧 Install and configure all tools (Prometheus, Grafana, Node Exporter) via **Ansible**
- 🐍 Deploy a custom **Flask microservice** exposing Prometheus metrics
- 📊 Build **custom Docker images manually** (no DockerHub images allowed)
- 🛠 Monitor **system metrics**, **application metrics**, and **container performance**
- 📡 Visualize real-time data using **Grafana dashboards**

---

## ⚙️ Tools & Technologies

- **Ansible** – automation of Docker setup and container deployment
- **Docker** – containerization of microservice and monitoring stack
- **Prometheus** – metrics collection and alerting
- **Grafana** – data visualization
- **Node Exporter** – hardware and OS metrics
- **cAdvisor** – Docker container metrics
- **nginx-prometheus-exporter** – export NGINX stats
- **Flask** – lightweight Python web microservice

---

## 🚀 Step-by-Step Commands

---

### ✅ 1. Launch EC2 & Open Ports

- TCP: `22`, `80`, `3000`, `5000`, `8080`, `9090`, `9100`, `9113`

---

### ✅ 2. SSH into EC2

```bash
ssh -i ~/.ssh/key.pem ubuntu@<EC2_PUBLIC_IP>
sudo apt update && sudo apt upgrade -y
````

---

### ✅ 3. Setup Ansible Control Node

```bash
sudo apt update
sudo apt install ansible python3-pip -y
```

---

### ✅ 4. Create Project Structure

```bash
mkdir -p monitoring-project/{prometheus,grafana,node_exporter,microservice}
mkdir -p monitoring-project/ansible/roles/{docker,prometheus,node_exporter,grafana}/tasks
cd monitoring-project
touch ansible/{inventory,playbook.yml}
```

---

### ✅ 5. Run Ansible Playbook

```bash
cd monitoring-project/ansible
ansible-playbook -i inventory playbook.yml
```

---

### ✅ 6. Build and Run Flask App Manually

```bash
cd microservice
docker build -t flask-app .
docker run -d -p 5000:5000 flask-app
```

---

### ✅ 7. Pull and Run NGINX App

```bash
docker pull kiran22222/nginx-web-app-rk:v1
docker run -d -p 80:80 --name nginx-web-app kiran22222/nginx-web-app-rk:v1
```

---

### ✅ 8. Enable NGINX Status (Inside Container)

```bash
docker exec -it nginx-web-app sh
vi /etc/nginx/conf.d/default.conf

# Add:
location /nginx_status {
    stub_status;
    allow all;
}

nginx -s reload
```

---

### ✅ 9. Get NGINX Container IP

```bash
docker inspect nginx-web-app | grep IPAddress
```

---

### ✅ 10. Run NGINX Prometheus Exporter

```bash
docker run -d \
  --name nginx-exporter \
  -p 9113:9113 \
  nginx/nginx-prometheus-exporter:1.1.0 \
  -nginx.scrape-uri http://<NGINX_CONTAINER_IP>/nginx_status
```

---

### ✅ 11. Run cAdvisor

```bash
docker run -d \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:rw \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --publish=8080:8080 \
  gcr.io/cadvisor/cadvisor:latest
```

---

### ✅ 12. Access Web Interfaces

| Service       | URL                             |
| ------------- | ------------------------------- |
| Flask App     | http\://\<EC2\_IP>:5000         |
| Flask Metrics | http\://\<EC2\_IP>:5000/metrics |
| Prometheus    | http\://\<EC2\_IP>:9090         |
| Node Exporter | http\://\<EC2\_IP>:9100/metrics |
| Grafana       | http\://\<EC2\_IP>:3000         |
| cAdvisor      | http\://\<EC2\_IP>:8080         |
| NGINX Metrics | http\://\<EC2\_IP>:9113/metrics |

---

### ✅ 13. Setup Grafana Dashboards

1. Visit: `http://<EC2_IP>:3000`
2. Login: `admin / admin`
3. Add Prometheus data source: `http://<EC2_IP>:9090`
4. Import Dashboards:

   * Node Exporter: `1860`
   * NGINX Exporter: `12708`
   * cAdvisor: `14282` (optional)
   * Custom Flask App: Use metric `app_requests_total`

---

## ✅ Project Complete!

You now have:

✅ A monitored Flask microservice
✅ Custom-built monitoring stack
✅ Real-time dashboards
✅ Automated with Ansible
✅ No DockerHub dependencies

---

## 👤 Author

**Kiran Rakh**
DevOps Engineer | Pune
📧 [kiranrakh155@gmail.com](mailto:kiranrakh155@gmail.com)
🔗 [LinkedIn](https://www.linkedin.com/in/kiran-rakh)



