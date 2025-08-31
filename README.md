# 🚀 n8n Setup Guide (Windows)

This guide explains **two ways** to install and run n8n:  
1. **Docker (recommended for teams & production)**  
2. **npm (simpler, good for testing/development)**  

---

## 📌 Prerequisites

- **For Docker setup**  
  - [Docker Desktop](https://www.docker.com/products/docker-desktop) installed  
  - WSL2 enabled  

- **For npm setup**  
  - [Node.js (LTS)](https://nodejs.org/en/download/) installed  
  - npm installed with Node.js  

---

## ⚡ Option 1: Setup n8n with Docker (Recommended)

### 1. Create project folder
```powershell
mkdir "n8n-server\n8n"
cd "n8n-server\n8n"
```
### 2. Create .env file
```bash
# ----- n8n base URLs -----
N8N_HOST=   # Use your LAN IP so other devices can access
N8N_PORT=5678
N8N_PROTOCOL=http
WEBHOOK_URL=http://<Your LAN IP>/

# ----- login gate for the UI -----
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER= <Your Username>
N8N_BASIC_AUTH_PASSWORD= <Your Password>

# ----- database (Postgres) -----
DB_TYPE=postgresdb
DB_POSTGRESDB_HOST=postgres
DB_POSTGRESDB_PORT=5432
DB_POSTGRESDB_DATABASE=n8n
DB_POSTGRESDB_USER=n8n
DB_POSTGRESDB_PASSWORD= <Your Password>

# ----- timezone & runners -----
GENERIC_TIMEZONE=Asia/Dhaka
N8N_RUNNERS_ENABLED=true
N8N_SECURE_COOKIE=false
```
### 3. Create docker-compose.yml
```bash
version: "3.8"
services:
  postgres:
    image: postgres:14
    restart: always
    environment:
      POSTGRES_USER: ${DB_POSTGRESDB_USER}
      POSTGRES_PASSWORD: ${DB_POSTGRESDB_PASSWORD}
      POSTGRES_DB: ${DB_POSTGRESDB_DATABASE}
    volumes:
      - c:/n8n/data/postgres:/var/lib/postgresql/data

  n8n:
    image: n8nio/n8n:latest
    restart: always
    env_file:
      - ./.env
    ports:
      - "5678:5678"
    volumes:
      - c:/n8n/data/n8n:/home/node/.n8n
    depends_on:
      - postgres
```
### 4. Start n8n
```bash
docker compose up -d
```
Open in browser:
```bash
http://<Your IPV4 Address>:5678
```
Login with username and password.
### 5. Verify running containers
```bash
docker ps
```

## ⚡ Option 2: Setup n8n with npm (Quick Test)
### 1. Install n8n globally
```powershell
npm install -g n8n
```
### 2. Start n8n
```powershell
n8n
```
A browser window will open asking you to sign up and create your account.
### 3. Create .env file
```env
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER= <Your Username>
N8N_BASIC_AUTH_PASSWORD= <Your Password>
```
### 4. Restart n8n
```powershell
n8n
```
## 🔥 Firewall Setup (LAN Access)
If you want others in your office LAN to access n8n:
1. Press Win + R, type wf.msc, hit Enter.
2. Go to Inbound Rules → New Rule.
3. Select Port → TCP → Specific local ports: 5678.
4. Choose Allow the connection.
5. Apply to Domain, Private, Public.
6. Name it: n8n port 5678.

Now people can access:
```bash
http://<YOUR_LAN_IP>:5678
```
## ✅ Testing persistence
Run:
```powershell
docker compose down
docker compose up -d
```
Verify your workflows are still saved (Postgres stores them).
