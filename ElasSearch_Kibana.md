# Elasticsearch + Kibana Setup on CentOS VM
Tested on Elasticsearch 8.x + CentOS

---

## 1. Install Elasticsearch

```bash
sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

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

sudo yum install elasticsearch -y
```

---

## 2. Install Kibana

```bash
sudo tee /etc/yum.repos.d/kibana.repo << EOF
[kibana-8.x]
name=Kibana repository for 8.x packages
baseurl=https://artifacts.elastic.co/packages/8.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
EOF

sudo yum install kibana -y
```

---

## 3. Set Bootstrap Password (before first start)

```bash
echo "Elastic123!" | sudo /usr/share/elasticsearch/bin/elasticsearch-keystore add -x "bootstrap.password"
```

---

## 4. Write Clean Elasticsearch Config

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

---

## 5. Start Elasticsearch

```bash
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch
sleep 40
```

### Health Check
```bash
curl -u elastic:Elastic123! http://localhost:9200/_cluster/health?pretty
```
Wait until `"status": "green"` or `"yellow"` before continuing.

---

## 6. Set kibana_system Password

```bash
curl -u elastic:Elastic123! \
  -X POST "http://localhost:9200/_security/user/kibana_system/_password" \
  -H "Content-Type: application/json" \
  -d '{"password": "Kibana123!"}'
```

Expected response: `{}`

---

## 7. Write Clean Kibana Config

```bash
sudo bash -c 'cat > /etc/kibana/kibana.yml << EOF
server.port: 5601
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://localhost:9200"]
elasticsearch.username: "kibana_system"
elasticsearch.password: "Kibana123!"
EOF'
```

---

## 8. Start Kibana

```bash
sudo systemctl enable kibana
sudo systemctl restart kibana
sleep 30
```

### Health Check
```bash
sudo systemctl status kibana --no-pager -n 5
```

---

## 9. Open in Browser

```bash
# Get your VM IP
ip addr show | grep "inet " | grep -v 127.0.0.1
```

Then open: `http://<your-vm-ip>:5601`

Login with:
- **Username:** `elastic`
- **Password:** `Elastic123!`

---

## Quick Health Checks

```bash
# Is Elasticsearch running?
sudo systemctl status elasticsearch --no-pager -n 3

# Is port 9200 open?
sudo ss -tlnp | grep 9200

# Is cluster healthy?
curl -u elastic:Elastic123! http://localhost:9200/_cluster/health?pretty

# Is Kibana running?
sudo systemctl status kibana --no-pager -n 3

# Kibana errors?
sudo journalctl -u kibana -n 30 --no-pager
```

---

## Firewall (if needed)

```bash
sudo firewall-cmd --permanent --add-port=9200/tcp
sudo firewall-cmd --permanent --add-port=5601/tcp
sudo firewall-cmd --reload
```

---

## Key Notes
- **SVE warnings** are harmless — ignore them
- **Transport SSL must be enabled** when security is enabled (bootstrap check requirement)
- **HTTP SSL is disabled** so you can use plain `http://` for curl and Kibana
- **sudo password** = your CentOS login password, NOT the Elasticsearch password
- If config is broken, always use `sudo bash -c 'cat > file << EOF'` to rewrite it (avoids permission issues)
