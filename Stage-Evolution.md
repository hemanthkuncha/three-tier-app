## 🔐 SSL Management Strategy: The 4-Stage Evolution

This project followed a step-by-step evolution from manual networking to full cloud-native automation.

### 1. HTTP-01 via NodePort (The "Manual Networking" Stage)
* **Process:** Attempted to get a Let's Encrypt certificate before using an Ingress Controller or cert-manager.
* **Implementation:** Tried to expose the application via **NodePort** on Port 80 to satisfy the ACME HTTP-01 challenge.
* **Lesson Learned:** Extremely difficult to manage manually. ISP Port 80 blocks and NAT issues made this "Hard Mode" networking, leading to the search for a more automated solution.

### 2. Manual OpenSSL (The "Local Encryption" Stage)
* **Process:** Generated a Private Key and a Certificate manually using the `openssl` CLI on the Ubuntu host.
* **Implementation:** Manually created a Kubernetes TLS secret to enable HTTPS:  
    `kubectl create secret tls manual-tls-secret --cert=server.crt --key=server.key`
* **Result:** Enabled encryption, but triggered "Not Secure" browser warnings because the certificate was not signed by a trusted authority.

### 3. Automated Self-Signed (The "Cert-Manager Intro" Stage)
* **Process:** Introduced **cert-manager** to handle the lifecycle of certificates within the cluster.
* **Implementation:** Configured a `SelfSigned` ClusterIssuer and a `Certificate` YAML.
* **Result:** Automated the creation and renewal of Secrets. This was the first step into "GitOps" style security, though still untrusted by public browsers.

### 4. DNS-01 Challenge (The "Production" Stage) - **CURRENT**
* **Process:** Switched to the **DNS-01 challenge** using the **DuckDNS Webhook** to achieve full public trust.
* **Implementation:** Used a `ClusterIssuer` with a DuckDNS API token to verify domain ownership via DNS TXT records.
* **Result:** **🏆 SUCCESS (Green Padlock)**. This is the final production state:
    * No need to open Port 80.
    * Works behind home routers/NAT.
    * Certificates are fully trusted by all browsers.
