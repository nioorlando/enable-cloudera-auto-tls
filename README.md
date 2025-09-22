# Enable Auto‑TLS in Cloudera Manager (Standalone)

Step‑by‑step instructions to enable **Auto‑TLS** for Cloudera Manager and clusters using the built‑in CA, suitable for single‑ or multi‑node environments.

## Contents
- [`enable_auto_tls.md`](enable_auto_tls.md) — full step‑by‑step guide
- `images/` — put your screenshots here and update the links inside the docs

## Prerequisites
- Cloudera Manager **Server & Agent installed** and the cluster is healthy
- **FQDNs are consistent** (shortname ↔ FQDN resolve correctly across nodes)
- **Kerberos enabled** (if used in your environment) and working
- **SSH access** to all hosts (either *root password* or *root private key*)

## Quick start
1. In CM UI: **Administration → Security → Enable Auto‑TLS** → *Generate CA*  
2. **Restart CM Server** on the host and wait for Jetty to start  
3. **Restart Management Services** from CM UI  
4. Ensure every Agent points to the **FQDN** (not `localhost`) and **restart the Agent**  
5. **Restart all cluster services** from CM UI and verify TLS is enabled

> Full details and troubleshooting are in [`enable_auto_tls.md`](enable_auto_tls.md).
