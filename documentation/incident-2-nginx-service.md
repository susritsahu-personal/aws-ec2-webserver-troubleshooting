# Incident 2: Nginx Service Failure

## Scenario

The Nginx service was intentionally stopped on the EC2 instance to simulate a web server service failure.

The objective was to identify whether the website connectivity problem was caused by AWS networking or by the application service running inside the instance.

## Symptoms

- Website was inaccessible from the browser.
- Local request to the web server failed.
- EC2 instance remained accessible through SSH.

## Investigation

### 1. Tested the Application Locally

```bash
curl http://localhost
```

The connection failed, indicating that the web service was not responding locally.

### 2. Checked Nginx Service Status

```bash
sudo systemctl status nginx
```

The service status showed:

```text
Active: inactive (dead)
```

This confirmed that Nginx was not running.

### 3. Checked Port 80

```bash
sudo ss -tulpn | grep :80
```

No listening process was returned for port 80.

### 4. Reviewed Nginx Logs

```bash
sudo journalctl -u nginx --since "10 minutes ago"
```

The logs showed that the Nginx service had been stopped and deactivated.

## Root Cause

The Nginx service was stopped, causing the web server to stop listening on HTTP port 80.

## Resolution

Started the Nginx service:

```bash
sudo systemctl start nginx
```

## Verification

After starting Nginx:

- `systemctl status nginx` showed the service as running.
- Nginx was listening on port 80 again.
- `curl http://localhost` returned the Nginx page.
- The website became accessible externally again.

## Troubleshooting Approach

The incident was investigated using:

**Local Connectivity → Service Status → Listening Port → Logs → Service Restoration → Verification**

## Key Learning

A website connectivity failure can originate from the application or service layer even when the EC2 instance itself is reachable. Testing the application locally and checking the service status helps distinguish a Linux service failure from an AWS network or Security Group issue.
