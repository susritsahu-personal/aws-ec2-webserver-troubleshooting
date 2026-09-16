# Incident 4: Increased Disk Utilization

## Scenario

Disk utilization was intentionally increased on the EC2 instance to simulate a storage-related incident.

The objective was to identify increased filesystem usage, locate the file consuming disk space, remove the unnecessary file, and verify recovery.

## Symptoms

- Root filesystem utilization increased from its normal level.
- Additional disk space was being consumed by a large temporary file.
- The Nginx web server remained accessible during the test.

## Investigation

### 1. Checked Baseline Disk Utilization

```bash
df -h
```

The root filesystem initially showed approximately:

- Total size: 8 GB
- Used: 1.8 GB
- Available: 6.3 GB
- Utilization: 22%

### 2. Generated Test Disk Utilization

A temporary 2 GB file was created to simulate increased disk usage:

```bash
fallocate -l 2G ~/large-test-file
```

### 3. Checked Disk Utilization Again

```bash
df -h
```

After creating the test file, root filesystem utilization increased to approximately 47%.

### 4. Identified the Large File

```bash
du -ah ~ 2>/dev/null | sort -rh | head -10
```

The output identified:

```text
/home/ec2-user/large-test-file
```

as the approximately 2 GB file consuming the additional disk space.

## Root Cause

The intentionally created `large-test-file` was consuming approximately 2 GB of disk space and caused filesystem utilization to increase.

## Resolution

The temporary test file was removed:

```bash
rm ~/large-test-file
```

## Verification

Disk utilization was checked again:

```bash
df -h
```

After removing the file:

- Root filesystem utilization returned to approximately 22%.
- The temporary 2 GB file was no longer present.
- The Nginx web server continued operating normally.
- `curl http://localhost` confirmed that the web service remained accessible.

## Troubleshooting Approach

The incident was investigated using:

**Filesystem Utilization → Disk Usage Analysis → Large File Identification → File Removal → Disk Verification → Service Verification**

## Key Learning

High disk utilization should be investigated before deleting files. Tools such as `df` help identify which filesystem is consuming space, while `du` helps locate directories and files responsible for the usage.

After corrective action, both disk utilization and application health should be verified to confirm
