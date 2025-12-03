# Access a Firewall and Create a Rule (Google Cloud Lab)

This repository documents a hands-on Google Cloud lab where I created and tested VPC firewall rules to control access to a web server VM and then analyzed the traffic using Logs Explorer.

## Objective

- View existing VPC firewall rules in a Google Cloud project.
- Create an **allow** firewall rule for HTTP and SSH traffic to a web server.
- Verify that the web page is accessible from the internet.
- Create a **deny** rule to block HTTP traffic.
- Use **Logs Explorer** to view and filter both allowed and denied traffic.

---

## Lab Environment

- Platform: **Google Cloud (Qwiklabs project)**
- Services used:
  - Network Security → Firewall policies
  - VPC firewall rules
  - Compute Engine → VM instances
  - Cloud Logging → Logs Explorer (VPC Flow Logs & Firewall Logs)

---

## Steps Performed

### 1. View existing firewall rules

1. Open the Google Cloud Console.
2. Go to **Network Security → Firewall policies**.
3. Under **VPC firewall rules**, review the default rules such as:
   - `default-allow-icmp`
   - `default-allow-internal`
   - `default-allow-rdp`
   - `default-allow-ssh`

📸 `screenshots/01-firewall-policies-default-rules.png`

---

### 2. Create an allow firewall rule (allow-http-ssh)

1. In **Firewall policies**, click **Create firewall rule**.
2. Configure the rule:
   - **Name:** `allow-http-ssh`
   - **Logs:** On
   - **Network:** `vpc-net`
   - **Priority:** `1000`
   - **Direction of traffic:** Ingress
   - **Action on match:** Allow
   - **Targets:** Specified target tags (for the web server)
   - **Source filter:** IP ranges `0.0.0.0/0`
   - **Protocols and ports:** `tcp:22,80`
3. Click **Create** to save the rule.

📸 `screenshots/02-create-firewall-rule-allow-http-ssh.png`  
📸 `screenshots/03-firewall-rule-list-with-allow-http-ssh.png`

---

### 3. Verify access to the web server VM

1. Go to **Compute Engine → VM instances**.
2. Locate the `web-server` instance and copy its **external IP address**.
3. From a browser, open:
   - `http://<external-ip>`
4. Confirm that the **Cymbal Group** test website loads successfully.

📸 `screenshots/04-vm-instance-web-server.png`  
📸 `screenshots/05-webpage-opened-successfully.png`

This confirms that the `allow-http-ssh` rule is working.

---

### 4. Analyze allowed traffic in Logs Explorer

1. Open **Logging → Logs Explorer**.
2. Filter logs for VPC/subnetwork traffic:
   - **Resource type:** `gce_subnetwork`
   - (Optional) Log name: `vpc_flows`
3. Run the query and review entries where `disposition = "ALLOWED"`.

📸 `screenshots/06-logs-explorer-allowed-traffic.png`  
📸 `screenshots/07-logs-explorer-subnetwork-traffic.png`  
📸 `screenshots/08-logs-explorer-vpc-flows.png`

---

### 5. Create a deny firewall rule (deny-http)

1. Go back to **Network Security → Firewall policies** and click **Create firewall rule** again.
2. Configure the rule:
   - **Name:** `deny-http`
   - **Logs:** On
   - **Network:** `vpc-net`
   - **Priority:** `1000` (or higher priority than the allow rule, depending on lab instructions)
   - **Direction of traffic:** Ingress
   - **Action on match:** Deny
   - **Targets:** Same web server tag
   - **Source filter:** IP ranges `0.0.0.0/0`
   - **Protocols and ports:** `tcp:80`
3. Click **Create** to save the deny rule.

📸 `screenshots/09-create-firewall-rule-deny-http.png`

---

### 6. Confirm HTTP is blocked and view denied logs

1. Try to open the web server again in the browser using the same external IP.
2. The HTTP request should now fail (blocked by firewall).
3. In **Logs Explorer**, filter:
   - **Resource type:** `gce_subnetwork`
   - **Log name:** `compute.googleapis.com/firewall`
   - Optionally filter on the web server IP.
4. Look for entries with `disposition = "DENIED"` for port 80.

📸 `screenshots/10-logs-explorer-denied-traffic.png`

---

## What I Learned

- How VPC firewall rules control **ingress** traffic to instances.
- The difference between **allow** and **deny** rules and the role of **priority**.
- How to target specific instances using **network tags**.
- How to use **Logs Explorer**,
