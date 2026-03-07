# 🔍 Elasticsearch + Kibana on CentOS VM

![Elasticsearch](https://img.shields.io/badge/Elasticsearch-8.19.12-005571?style=for-the-badge&logo=elastic&logoColor=white)
![Kibana](https://img.shields.io/badge/Kibana-8.19.12-E8478B?style=for-the-badge&logo=elastic&logoColor=white)
![CentOS](https://img.shields.io/badge/CentOS-Stream-262577?style=for-the-badge&logo=centos&logoColor=white)
![Status](https://img.shields.io/badge/Status-Working-brightgreen?style=for-the-badge)

A complete, battle-tested guide to deploying Elasticsearch and Kibana on a CentOS VM with security enabled. This setup was achieved through hands-on troubleshooting and documents every real-world obstacle encountered along the way.

---

## 🏆 What We Achieved

| Component | Version | Status |
|---|---|---|
| Elasticsearch | 8.19.12 | ✅ Running |
| Kibana | 8.19.12 | ✅ Running |
| Security (xpack) | Enabled | ✅ Authenticated |
| Cluster Health | Green | ✅ 100% shards active |
| HTTP Access | Plain HTTP + Secure Transport | ✅ Working |

---

## 📁 Files in This Repo

```
├── elasticsearch-kibana-setup.ipynb   # Jupyter notebook with all commands
├── README.md                          # This file
```

---

## ⚡ Quick Start

### Prerequisites
- CentOS VM with minimum **4GB RAM**
- sudo access
- Internet connection

### 1. Install

```bash
# Import GPG key
sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

# Add repo and install Elasticsearch
sudo tee /etc/yum.repos.d/elasticsearch.repo << EOF
[elasticsearch]
name=Elasticsearch repository for 8.x packages
baseurl=https://artifacts.elastic.co/packages/8.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
EOF
sudo yum install elasticsearch kibana -y
```

### 2. Set Bootstrap Password (BEFORE first start)

```bash
echo "YourPassword123!" | sudo /usr/share/elasticsearch/bin/elasticsearch-keystore add -x "bootstrap.password"
```

### 3. Configure Elasticsearch

```bash
sudo bash -c 'cat > /etc/elasticsearch/elasticsearch.yml << EOF
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
http.port: 9200
network.host: 0.0.0.0
cluster.initial_master_nodes: ["localhost.localdomain"]
xpack.security.enabled: true
xpack.security.enrollment.enabled: false
xpack.security.http.ssl.enabled: false
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: certs/transport.p12
xpack.security.transport.ssl.truststore.path: certs/transport.p12
EOF'
```

### 4. Start & Verify Elasticsearch

```bash
sudo systemctl enable elasticsearch && sudo systemctl start elasticsearch && sleep 40
curl -u elastic:YourPassword123! http://localhost:9200/_cluster/health?pretty
```

Wait for `"status": "green"` before continuing.

### 5. Configure & Start Kibana

```bash
# Set kibana_system password
curl -u elastic:YourPassword123! \
  -X POST "http://localhost:9200/_security/user/kibana_system/_password" \
  -H "Content-Type: application/json" \
  -d '{"password": "KibanaPassword123!"}'

# Write Kibana config
sudo bash -c 'cat > /etc/kibana/kibana.yml << EOF
server.port: 5601
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://localhost:9200"]
elasticsearch.username: "kibana_system"
elasticsearch.password: "KibanaPassword123!"
EOF'

# Start Kibana
sudo systemctl enable kibana && sudo systemctl restart kibana
```

### 6. Open Kibana

```bash
ip addr show | grep "inet " | grep -v 127.0.0.1
```

Go to `http://<your-vm-ip>:5601` and login with `elastic` / `YourPassword123!`

---

## 🧱 Architecture

```
┌─────────────────────────────────────┐
│           CentOS VM                 │
│                                     │
│  ┌─────────────┐  ┌──────────────┐  │
│  │ Kibana      │  │Elasticsearch │  │
│  │ :5601       │─▶│ :9200        │  │
│  │ (HTTP)      │  │ (HTTP)       │  │
│  └─────────────┘  └──────────────┘  │
│                        │            │
│                   Transport SSL     │
│                   :9300             │
└─────────────────────────────────────┘
         │
    Browser access
    http://<vm-ip>:5601
```

---

## 🚧 Troubleshooting Lessons Learned

### 1. Duplicate config key crashes Elasticsearch
If you see `Duplicate field 'xpack.security.enabled'` always rewrite the entire config file rather than appending:
```bash
sudo bash -c 'cat > /etc/elasticsearch/elasticsearch.yml << EOF
... your config ...
EOF'
```

### 2. Transport SSL is mandatory when security is enabled
Even if you disable HTTP SSL, transport SSL must stay on or Elasticsearch will fail the bootstrap check with exit code 78.

### 3. Bootstrap password must be set before first start
The `elasticsearch-reset-password` tool requires a healthy cluster to work. Set the password via keystore before the first start to avoid being locked out.

### 4. sudo password ≠ Elasticsearch password
When prompted `[sudo] password for <user>:` always enter your CentOS system login password, not your Elasticsearch password.

### 5. Always wait for cluster green before setting passwords
The security index isn't ready until the cluster is fully initialized. Running password commands too early returns a 503 error.

---

## 🩺 Health Check Commands

```bash
# Full stack check
echo "=== ES Status ===" && sudo systemctl status elasticsearch --no-pager -n 3
echo "=== Ports ===" && sudo ss -tlnp | grep -E "9200|5601"
echo "=== Cluster Health ===" && curl -u elastic:YourPassword123! http://localhost:9200/_cluster/health?pretty
echo "=== Kibana Status ===" && sudo systemctl status kibana --no-pager -n 3
```

---

## 📌 Key Notes

- SVE warnings from OpenJDK are harmless on ARM VMs — ignore them
- Use `sudo bash -c 'cat > file'` pattern to write protected config files
- Always use `--no-pager` with journalctl to see full output
- Elasticsearch needs 30-60 seconds to fully initialize after start

---

## 📅 Date Completed
March 7, 2026
