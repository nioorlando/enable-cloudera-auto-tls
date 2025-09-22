# Enable Auto‑TLS in Cloudera Manager (Standalone)

This guide enables **Auto‑TLS** using Cloudera Manager’s built‑in CA and distributes certificates to all hosts.

---

## 1) Prerequisites & Checklist
- CM Server & Agents installed, cluster healthy
- Hostname & **FQDN are consistent** and resolvable
- **Kerberos running** (if used) and valid
- SSH access to all hosts (choose: same **root password** or **private key**)

---

## 2) Enable Auto‑TLS from CM UI
1. Open **CM UI → Administration → Security → Enable Auto‑TLS**  
2. **Step 1 of 2 – Generate CA**. Fill the form:
   - **Enable TLS for**: *All existing and future clusters*
   - **SSH Username**: `root`
   - **Authentication Method**: *All hosts accept same password* (or choose *Private Key* when applicable)
   - **Password**: `(password root node)`  
   - **Confirm Password**: `(confirm password)`  
   - **SSH Port**: `22`
3. Click **Generate CA** and wait until it finishes.
4. Proceed to **Step 2 of 2** in the wizard.

> Put screenshots under `images/` and reference them here, e.g.  
> `![Enable Auto‑TLS](images/enable_auto_tls_step1.png)`

---

## 3) Restart Cloudera Manager Server (SSH)
On the CM Server host:
```bash
sudo systemctl restart cloudera-scm-server
tail -f /var/log/cloudera-scm-server/cloudera-scm-server.log
```
Wait until you see the Jetty start line (example):
```text
INFO  jetty.Server: Started Jetty server
```

---

## 4) Restart Management Services (CM UI)
- Go to **Administration → Management Services → Restart**.

---

## 5) Ensure CM Agent uses FQDN & Restart Agent
Edit the Agent config and set the CM Server **FQDN** (not `localhost`):
```bash
sudo vi /etc/cloudera-scm-agent/config.ini
```
Change:
```ini
server_host=(fqdn-hostname)
```
Restart the Agent:
```bash
sudo systemctl restart cloudera-scm-agent
```

---

## 6) Restart All Cluster Services (CM UI)
- In the CM UI, restart the cluster so services pick up TLS.

---

## 7) Verify TLS
Agent log (no hostname/SAN mismatch errors):
```bash
sudo tail -f /var/log/cloudera-scm-agent/cloudera-scm-agent.log
```
Example success lines:
```text
INFO  agent.py: Successfully connected to Cloudera Manager Server at (fqdn-hostname):7182
INFO  https.py: SSL connection established with valid hostname verification
```
Also check **CM UI → Administration → Security → TLS Status** shows *Enabled*.

---

## 8) Troubleshooting
- **Peer certificate subjectAltName does not match host**  
  → Ensure OS hostname, `/etc/hosts`, DNS, and `server_host` in the Agent config all match the certificate SAN.  
  → Fix mappings, then restart **Agent** and **CM Server**.
- **Cert distribution fails**  
  → Verify SSH method (root password vs private key) is valid for *all hosts*.
- **Kerberos environment**  
  → Kerberos must remain healthy while switching to TLS.
- After hostname or config changes, **restart CM Server and Agents**.

---

## 9) Validation Checklist
- CM Server log shows Jetty started after TLS changes
- CM Agent connects to **7182** with SSL and no SAN errors
- CM UI reports **TLS Enabled**
- Cluster services are **green/healthy**

---

## Appendix — File paths & Notes
- CM Server log: `/var/log/cloudera-scm-server/cloudera-scm-server.log`
- CM Agent log: `/var/log/cloudera-scm-agent/cloudera-scm-agent.log`
- CM Agent config: `/etc/cloudera-scm-agent/config.ini`

Update screenshots references as needed (e.g., `images/auto_tls_step2.png`).

---
