# Task 27: Configure a Custom DNS Record Using AWS Route 53

## Overview

This project demonstrates private DNS record configuration using Amazon Route 53 and an Ubuntu EC2 instance as part of the Veda Technology Cloud Computing Internship.

The objective was to create a custom DNS record, associate it with a cloud-hosted resource, and verify successful DNS resolution.

## Objectives

- Understand DNS and how hostnames map to IP addresses.
- Create a private hosted zone in Amazon Route 53.
- Configure an A record for an EC2-hosted web server.
- Verify DNS resolution and HTTP connectivity.
- Document configuration, verification, and cleanup.

## AWS Services and Tools

- Amazon EC2
- Amazon Route 53
- Ubuntu Server
- Nginx
- SSH
- `getent`
- `curl`

## Architecture

```text
Ubuntu EC2 Instance
Private IP: 172.31.3.204
        |
        | Nginx HTTP service (port 80)
        |
Route 53 Private Hosted Zone
task27.internal
        |
        | A record
        v
web.task27.internal
        |
        v
172.31.3.204
```

## Configuration Details

| Component | Configuration |
|---|---|
| DNS service | Amazon Route 53 |
| Hosted zone | `task27.internal` |
| Hosted zone type | Private |
| DNS record | `web.task27.internal` |
| Record type | A |
| Record value | `172.31.3.204` |
| TTL | 300 seconds |
| Operating system | Ubuntu 26.04.1 LTS |
| Web server | Nginx |
| HTTP port | 80 |
| AWS Region | Asia Pacific (Mumbai), `ap-south-1` |

**Important:** `task27.internal` is a private DNS namespace used for this lab. It is not a publicly registered domain and is not expected to resolve from the public internet.

## Implementation Steps

### 1. Launch the EC2 instance

Launched an Ubuntu EC2 instance named `veda-task27-dns-server` and confirmed that the instance was running and its status checks passed.

### 2. Connect to Ubuntu

Connected using SSH and the EC2 key pair. SSH access was restricted to the administrator's public IP address.

### 3. Install Nginx

Updated package information and installed Nginx:

```bash
sudo apt update
sudo apt install nginx -y
sudo systemctl status nginx --no-pager
```

Confirmed that the Nginx service was active.

### 4. Create a test webpage

Created a custom HTML page at `/var/www/html/index.html` identifying the internship task.

Verified the page locally:

```bash
curl http://localhost
```

Verified that the webpage was reachable through the EC2 public IPv4 address using a browser.

### 5. Create the Route 53 private hosted zone

Created the private hosted zone `task27.internal` and associated it with the EC2 instance's VPC in the Mumbai Region.

### 6. Create the A record

Created the following DNS record:

- Record name: `web.task27.internal`
- Record type: `A`
- IPv4 address: `172.31.3.204`
- TTL: 300 seconds

### 7. Verify DNS resolution

Executed the following command from the EC2 instance:

```bash
getent hosts web.task27.internal
```

Expected and observed result:

```text
172.31.3.204  web.task27.internal
```

### 8. Verify HTTP response through the DNS name

Executed:

```bash
curl http://web.task27.internal
```

The command returned the HTML content of the custom test webpage, confirming that the private DNS name resolved to the EC2 instance and the Nginx web server responded.

## Verification Results

| Test | Expected result | Observed result |
|---|---|---|
| EC2 instance status | Running | Passed |
| Nginx service | Active and running | Passed |
| Local HTTP request | HTML response | Passed |
| Public IP browser test | Test webpage loads | Passed |
| Private DNS lookup | Resolves to `172.31.3.204` | Passed |
| HTTP request through DNS name | Returns test webpage HTML | Passed |

## DNS Concepts

### What is DNS?

The Domain Name System translates human-readable hostnames into IP addresses so clients can locate network resources.

### What is an A record?

An A record maps a hostname to an IPv4 address.

### What is a CNAME record?

A CNAME record maps one hostname to another hostname rather than directly to an IP address.

### Public versus private hosted zones

A public hosted zone provides DNS information intended for resolution through public DNS. A private hosted zone provides DNS resolution within its associated VPCs and supported connected networks.

This project used a private hosted zone because no publicly registered domain was available.

## Security Considerations

- SSH access was restricted to the administrator's public IP address.
- HTTP was allowed for testing the webpage.
- Private DNS was used instead of claiming public DNS availability.
- Private key files and AWS credentials must never be uploaded to GitHub.
- The EC2 instance and hosted zone should be removed after the practical if they are no longer needed.

## Cleanup and Rollback

After capturing the required evidence:

1. Delete the custom A record from the Route 53 private hosted zone.
2. Delete the `task27.internal` private hosted zone.
3. Terminate the `veda-task27-dns-server` EC2 instance.
4. Check for any separately created EBS volumes, snapshots, or Elastic IP addresses and remove unused resources where appropriate.
5. Confirm that no unnecessary billable resources remain.

Take care not to delete resources belonging to other tasks.

## Evidence

Add the task screenshots to the `screenshots/` folder:

1. `Task27_01_EC2_Instance_Running.png`
2. `Task27_02_Nginx_Service_Running.png`
3. `Task27_03_Nginx_Test_Page_Verification.png`
4. `Task27_04_Website_Accessible_Public_IP.png`
5. `Task27_05_Route53_Private_Hosted_Zone.png`
6. `Task27_06_Custom_DNS_A_Record.png`
7. `Task27_07_DNS_Resolution_Proof.png`
8. `Task27_08_DNS_Webpage_Verification.png`

## Conclusion

This practical demonstrated how to configure a private DNS A record using Amazon Route 53, associate it with an EC2-hosted web server, and verify DNS resolution and HTTP connectivity.

The exercise reinforced practical understanding of DNS records, private hosted zones, EC2 networking, Linux administration, and web-server verification.

