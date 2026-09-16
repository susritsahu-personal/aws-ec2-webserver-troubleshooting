# Incident 1: Website Inaccessible Due to Security Group

## Scenario

The HTTP inbound rule was intentionally removed from the EC2 Security Group to simulate a common connectivity incident where the Nginx web server is running correctly but the website cannot be accessed externally.

## Symptoms

- Website was inaccessible from the browser.
- External connection to the web server timed out.
- EC2 instance remained accessible through SSH.

## Investigation

### 1. Checked Nginx Service

```bash
sudo systemctl status nginx
```
Nginx was running successfully.

### 2. Verified Port 80

```bash
sudo ss -tulpn | grep :80
```

Nginx was listening on port 80.

### 3. Tested the Application Locally

```bash
curl http://localhost
```

The Nginx page responded successfully.

This confirmed that the application and web service were healthy inside the EC2 instance.

### 4. Checked AWS Network Access

Since Nginx was running, port 80 was listening, and the local request succeeded, the investigation moved to the AWS network/security layer.

The EC2 Security Group did not contain an inbound rule allowing HTTP traffic on TCP port 80.

## Root Cause

The HTTP TCP port 80 inbound rule was missing from the EC2 Security Group, preventing external HTTP traffic from reaching the instance.

## Resolution

Added the HTTP inbound Security Group rule:

- Type: HTTP
- Protocol: TCP
- Port: 80
- Source: 0.0.0.0/0

## Verification

After restoring the Security Group rule:

- The Nginx website became accessible from the browser.
- Local access continued to work.
- Nginx remained in the running state.

## Troubleshooting Approach

The incident was investigated layer by layer:

**Application/Service → Listening Port → Local Connectivity → AWS Security Group → Resolution → Verification**

## Key Learning

A website being inaccessible externally does not automatically mean the web service has failed. Testing the service locally helps separate application/server issues from AWS networking and Security Group issues.
