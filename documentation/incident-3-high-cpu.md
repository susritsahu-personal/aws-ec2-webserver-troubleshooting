# Incident 3: High CPU Utilization

## Scenario

High CPU utilization was intentionally generated on the EC2 instance to simulate a resource-utilization incident.

The objective was to identify the process consuming CPU resources, observe the utilization increase, terminate the test process, and verify recovery.

## Symptoms

- EC2 CPU utilization increased significantly.
- A CPU-intensive process was consuming most of the available CPU resources.
- The Nginx web server remained accessible during the test.

## Investigation

### 1. Checked Running Processes

```bash
ps aux --sort=-%cpu | head
```

The process list was reviewed to identify processes consuming the most CPU.

### 2. Generated Test CPU Load

A CPU-intensive test process was started using:

```bash
yes > /dev/null &
```

This intentionally generated high CPU utilization for troubleshooting practice.

### 3. Identified the High-CPU Process

```bash
ps aux --sort=-%cpu | head
```

The `yes` process appeared as the primary CPU-consuming process.

### 4. Monitored CPU Utilization

```bash
top
```

`top` confirmed that the test process was consuming significant CPU resources.

Amazon CloudWatch was also used to observe the increase in the EC2 `CPUUtilization` metric.

## Root Cause

The intentionally generated `yes` process was consuming high CPU resources on the EC2 instance.

## Resolution

The CPU-intensive test process was terminated after identifying it.

The process ID was identified during troubleshooting and stopped using:

```bash
kill <PID>
```

## Verification

After terminating the test process:

- `top` showed CPU utilization returning toward normal levels.
- The high-CPU process was no longer running.
- The Nginx web server continued responding successfully.
- `curl http://localhost` confirmed that the web service remained accessible.

## Troubleshooting Approach

The incident was investigated using:

**CPU Utilization → Process Identification → Resource Monitoring → Root Cause → Process Termination → Service Verification**

## Key Learning

High CPU utilization does not automatically mean that an application is unavailable. CPU metrics should be correlated with running processes and application health before taking corrective action.

Linux tools such as `ps` and `top`, combined with Amazon CloudWatch metrics, can help identify and investigate CPU-related incidents.
