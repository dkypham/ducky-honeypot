# Ducky Honeypot
Create a honeynet on AWS and monitor it using a SIEM (Splunk).

## Technologies
- **AWS**
- **TCPDump**
- **Wireshark**
- **Metasploit**
- **Splunk**
- **Python**
- **Parallelism**

## Steps

### 1. Create an AWS EC2 Instance
1. **Create an AWS Account** and go to the **EC2** dashboard.
2. **Create a Key Pair** to use for SSH access.
3. **Create an EC2 Instance**:
   - Choose **Amazon Linux** and instance type **t2.micro**.
   - Select your created key pair for login access.
4. **Configure Network Settings**:
   - Allow **SSH** access from your IP.
   - Add a rule for **Splunk** on **port 8000** from your IP.

5. **Allocate an Elastic IP**:
   - Go to **Actions > Networking > Manage IP Addresses**.
   - Click **Allocate Elastic IP address**.
   - Associate the Elastic IP with your running instance (the first Elastic IP associated with a running instance is free).

6.  Increase Storage to 30 GB:
   - Go to Elastic Block Store > Volumes in the EC2 dashboard.
   - Select the volume attached to your instance and click Modify Volume.
   - Increase the size to 30 GB (maximum free tier) and save changes.

### 2. Connect to Your EC2 Instance Using PuTTY
1. **Get the public IPv4 address** of your EC2 instance.
2. In PuTTY:
   - Use `ec2-user@<public-ipv4>` as the hostname.
   - Go to **Connection > SSH > Auth** and provide the `.ppk` private key file.
3. **Save and open the session** to connect.

### 3. Install Splunk on EC2
1. **Download Splunk**:
   - Go to the [Splunk Download page](https://www.splunk.com/en_us/download/splunk-enterprise.html) and copy the `wget` command for the Linux version.
   - Run the following commands on the EC2 instance:
     ```
     wget -O splunk-9.1.3-d95b3299fa65-Linux-x86_64.tgz "https://download.splunk.com/products/splunk/releases/9.1.3/linux/splunk-9.1.3-d95b3299fa65-Linux-x86_64.tgz"
     tar -xzvf splunk-9.1.3-d95b3299fa65-Linux-x86_64.tgz
     ```
2. **Start Splunk**:
   ```
   - **Set up the admin username and password** when prompted.

- **Access the Splunk UI**:
  - Open a browser and go to `http://<public-ipv4>:8000`.
  - **Note**: Use **HTTP**, not HTTPS.

- **Configure Splunk**:
  - Go to **Settings > Server Settings > General Settings**:
    - Change the **Splunk server name** (no spaces).
    - Enable **SSL**.
    - Set **Default Host Name** to appear in logs.
  - **Restart Splunk** from **Settings > Server Controls** to apply changes.

- **Enable Splunk to Start on Boot**:
  ```
  sudo ./splunk enable boot-start --accept-license

   cd splunk/bin
   ./splunk start
  ```
### 4. Install Cowrie Honeypot on EC2
1. **Update the system**
```
sudo yum update -y
```

2. **Install dependencies:**
```
sudo yum install -y git python3 python3-devel openssl-devel libffi-devel
```

3. **Create a user for Cowrie:**
```
sudo useradd -m -s /bin/bash cowrie
sudo su - cowrie
```

4. **Download Cowrie:**
```
git clone https://github.com/cowrie/cowrie
cd cowrie
```

5. **Set up the virtual environment:**
```
python3 -m venv cowrie-env
source cowrie-env/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

6. **Run Cowrie:**
```
bin/cowrie start
```

**To check of cowrie is running**
```
bin/cowrie status
```

**To exit the cowrie-env**
```
deactivate
```

**To switch back from cowrie to original user**
```
exit
```


# TODO 

## 1. Network Traffic Analysis
Develop a Python tool to capture and analyze network traffic, identifying common attack patterns, anomalies, and suspicious behavior.

- **Parallelism**: Process large amounts of network data efficiently.
- **Protocol Support**: Focus on HTTP/3, QUIC, WebSockets, and gRPC.

## 2. Security Automation
Create a Python script to automate routine SOC security tasks, such as log analysis, incident response actions, and reporting.

- **Docker Integration**: Use Docker containers for portability and ease of deployment.

## 3. Docker Integration
- **Scalability**: Deploy multiple honeypot containers to simulate a larger attack surface.
- **Automation**: Automate deployment and management of honeypot containers, simplifying setup of a network of honeypots (honeynet).

## 4. Parallelism with Python
- **Identify Concurrent Tasks**: Determine which tasks can be performed concurrently (e.g., handling network connections, logging events, analyzing payloads).
- **Threading**: For I/O-bound tasks, use Python’s threading module.
