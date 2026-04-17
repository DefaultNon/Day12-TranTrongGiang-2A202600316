# Deployment Guide: Railway + Redis

This guide explains how to deploy your AI Agent to Railway with a linked Redis service.

## Prerequisites
- A [Railway](https://railway.app/) account.
- Your project pushed to a GitHub repository.

## Steps

### 1. Provision Services on Railway
1. Go to your Railway Dashboard.
2. **New Project** → **Github Repo** → Select your repository.
3. Click on the **+ Add** button at the top right of the project view.
4. Select **Redis**. Railway will provision a Redis instance.

### 2. Configure Environment Variables
1. Click on your **Agent** service (the one from your repo).
2. Go to the **Variables** tab.
3. Add the following manually:
   - `AGENT_API_KEY`: Your custom key (e.g., `my-secret-agent-key`).
   - `OPENAI_API_KEY`: Your OpenAI API key.
4. Click **New Variable** → **Reference Variable**.
5. Select `REDIS_URL` from the Redis service. Railway handles the connection string automatically.

### 3. Verification
Once the deployment finishes:
1. Copy the public **Domain** from the Service Settings.
2. Test the health endpoint: `GET https://<your-url>/health`
   - You should see `"redis": "connected"` in the `checks` section.
3. If Redis is NOT connected, the app will show `"redis": "offline (using fallback)"`, but it will still function!

## Monitoring
- Check the **Logs** tab in Railway for service output.
- The logs will show if the app is successfully connecting to Redis or using the fallback.
