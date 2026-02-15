# Agent Zero on Zeabur

> **Purpose:** Deploy Agent Zero on Zeabur with persistent data and secure public access  
> **Last Updated:** February 15, 2026  
> **Compatibility:** Zeabur projects using Docker image or YAML template deployment

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Dashboard Deployment (Recommended)](#dashboard-deployment-recommended)
3. [Template-Based Deployment](#template-based-deployment)
4. [Post-Deploy Security and Setup](#post-deploy-security-and-setup)
5. [Validation Checklist](#validation-checklist)
6. [Operations](#operations)
7. [Troubleshooting](#troubleshooting)

---

## Prerequisites

- A Zeabur account and project
- Access to Zeabur service logs and networking settings
- LLM provider API key(s) for Agent Zero model configuration

---

## Dashboard Deployment (Recommended)

This follows the image-based path and avoids custom build steps.

### 1. Create a service from Docker image

In Zeabur, create a new service and select Docker image deployment.

- **Image:** `agent0ai/agent-zero:latest`  
  For production stability, pin to a specific tag once validated.

### 2. Configure networking

- Expose HTTP traffic for the service (container port `80`)
- Enable a public URL/domain for the web port

### 3. Configure persistence

Attach a persistent volume and mount it to:

```text
/a0/usr
```

This path is required for persistent chats, projects, settings, and memory.

### 4. Deploy and verify startup

After deployment, open the service logs and wait for successful startup.
Then open the generated public URL.

---

## Template-Based Deployment

This repository includes a Zeabur template:

```text
deploy/zeabur/agent-zero.template.yaml
```

### Deploy with Zeabur CLI

```bash
npx zeabur@latest template deploy -f deploy/zeabur/agent-zero.template.yaml
```

After deployment, verify networking is public on the `web` port and continue with the post-deploy setup below.

---

## Post-Deploy Security and Setup

### 1. Enable Agent Zero authentication immediately

Inside Agent Zero Web UI:

1. Open **Settings**
2. Go to **Authentication**
3. Set username and password
4. Save and re-test login flow

### 2. Configure model providers in UI

Set Chat/Utility/Embedding provider details and add provider API keys.
Save settings and run one prompt as a smoke test.

### 3. Optional: preconfigure auth via service environment variables

If you want auth enabled before first interactive login, set:

```env
AUTH_LOGIN=your_username
AUTH_PASSWORD=your_strong_password
```

---

## Validation Checklist

- Service is healthy and reachable over HTTPS
- Authentication is enabled and enforced
- One test prompt completes successfully
- Data persists after service restart/redeploy
- Persistent mount path remains `/a0/usr`

---

## Operations

### Upgrade

1. Change image tag (for example from `latest` to a pinned version)
2. Redeploy
3. Run smoke checks

### Rollback

1. Revert to previous known-good image tag
2. Redeploy
3. Confirm UI and persistence integrity

---

## Troubleshooting

### Service boots but UI is unavailable

- Confirm public networking is enabled on the HTTP web port
- Confirm the service is exposing container port `80`

### Data resets after restart

- Verify persistent volume is mounted to `/a0/usr` exactly
- Check for accidental mount at `/a0` or another directory

### Login is not enforced

- Set credentials in UI Authentication settings, or
- Set `AUTH_LOGIN` and `AUTH_PASSWORD` in Zeabur service env and redeploy
