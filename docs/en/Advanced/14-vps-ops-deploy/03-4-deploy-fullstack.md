---
title: "14.3.4 Deploying Frontend-Backend Separated Applications"
---

# 14.3.4 Deploying Frontend-Backend Separated Applications

> **Goal of this section**: Master the general deployment workflow for frontend-backend separated architectures, learn to use Claude Code to assist with reverse proxy configuration, applicable to any tech stack (Node.js / Python / PHP / Java / Go / .NET, etc.).

::: tip Difference from 14.3.2
- **14.3.2**: Suitable for "full-stack" frameworks like Next.js where frontend and backend are in one project, handled by a single container
- **This section (14.3.4)**: Suitable for "frontend-backend separated" architectures where frontend and backend are independent projects that need separate deployment

If your project uses Next.js/Nuxt or similar full-stack frameworks, go directly to 14.3.2. If your frontend is React/Vue and your backend is a separate API service (Node.js/Python/Java, etc.), then you need the approach in this section.
:::

Xiaoming's full-stack project has three parts: frontend, backend API, and database. "Previously on Vercel it was one project that handled everything, now I need to deploy them separately?"

The veteran said: "Vercel bundled these together for you, but on your own server, you need to understand their individual roles. The upside is—you have complete control over every layer."

He drew a diagram: "A frontend-backend separated architecture looks like this—"

## Frontend-Backend Separated Architecture

```
User Browser
    │
    ▼
  OpenResty (Reverse Proxy) :80
    │
    ├── /          → Frontend static files (HTML/CSS/JS)
    ├── /api/*     → Backend service (container port 3000/8000/8080...)
    │
    └── Backend Service
            │
            ▼
        Database (PostgreSQL/MySQL/MongoDB...)
```

Three services, each with its own responsibility:

- **OpenResty**: The single entry point facing external traffic, responsible for request routing
- **Backend Service**: Handles business logic and database operations (Node.js / Python / Java / Go, etc.)
- **Database**: Stores data

::: tip What is a Reverse Proxy?
A **reverse proxy** is like a "front desk receptionist":
- All user requests first arrive at the front desk (OpenResty)
- The front desk looks at the request path and decides who should handle it:
  - Accessing `/` → Returns the frontend page
  - Accessing `/api/users` → Forwards to the backend service
- Users feel like they're only dealing with one server, but multiple services may be working behind the scenes

**Benefits**:
1. Unified entry: Users only need to remember one domain
2. Avoids CORS: Frontend and API are under the same domain
3. Security: Backend services aren't directly exposed to the public internet
:::

Xiaoming drew a simple diagram: user requests first go to OpenResty, which checks the path—if it starts with `/api`, forward to the backend; otherwise, return the frontend page. "So Vercel was doing this behind the scenes too, it just hid everything from me."

They all run in Docker containers, communicating with each other through the container network.

## General Deployment Workflow

Regardless of your backend tech stack, the deployment workflow is similar:

### Step 1: Deploy the Database

Based on project requirements, install the appropriate database from the 1Panel App Store:

- **PostgreSQL**: Suitable for relational data, complex queries
- **MySQL**: Suitable for traditional web applications
- **MongoDB**: Suitable for document-based data
- **Redis**: Suitable for caching, session storage

After installation, create an application-specific database and user, and note down the connection information (hostname, port, username, password).

::: tip 1Panel Database Container Naming Convention
1Panel-created database containers follow the format `1Panel-{database-type}-{4-random-letters}`:
- PostgreSQL: `1Panel-postgresql-ukow`
- Redis: `1Panel-redis-w94p`
- MySQL: `1Panel-mysql-abcd`
- MongoDB: `1Panel-mongodb-xyz1`

**How to check the actual container name**:
In 1Panel's "Containers" page, the first column of the list shows the container name. You can also click "Details" for the corresponding database in the "Database" page to view connection information.

Backend applications connect to the database using the **container name** (not `localhost`).
:::

::: details Common Database Connection String Examples

**PostgreSQL**:
```bash
DATABASE_URL="postgresql://username:password@1Panel-postgresql-ukow:5432/database_name"
```

**MySQL**:
```bash
DATABASE_URL="mysql://username:password@1Panel-mysql-abcd:3306/database_name"
```

**Redis (with password)**:
```bash
REDIS_URL="redis://:password@1Panel-redis-w94p:6379"
```

**Redis (no password)**:
```bash
REDIS_URL="redis://1Panel-redis-w94p:6379"
```

**MongoDB**:
```bash
MONGODB_URI="mongodb://username:password@1Panel-mongodb-xyz1:27017/database_name"
```
:::

### Step 2: Deploy the Backend Service

Choose the deployment method based on your backend tech stack:

| Tech Stack | 1Panel Runtime (Language) | Startup Command Reference |
|--------|---------|-------------|
| Node.js / Next.js | Node.js | `git pull && pnpm build && pnpm start` |
| Python / FastAPI | Python | `git pull && pip install -r requirements.txt && uvicorn main:app --host 0.0.0.0` |
| PHP / Laravel | PHP | PHP-FPM auto-starts, accessed through OpenResty reverse proxy |
| Java / Spring Boot | Java | `java -jar app.jar` |
| Go | Go | `./app` |
| .NET / ASP.NET Core | .NET | `dotnet run --urls http://0.0.0.0:5000` |

::: tip Runtime = Docker Container
1Panel's "Runtime" is essentially a Docker container. Each language (Node.js, Python, PHP, Java, Go, .NET) corresponds to a pre-configured base image. After you select the language and version, 1Panel uses docker-compose to manage container creation and startup for you.
:::

**Key Configurations**:

1. **Environment Variables**: Database connection strings, secrets, etc.
2. **Port Mapping**: In advanced settings, map the container's internal port (e.g., 3000) to an external server port (e.g., 3001)
3. **Startup Command**: Ensure latest code is automatically pulled on each restart
4. **Container Name**: Used for inter-container communication (e.g., reverse proxy forwards requests via container name)

Xiaoming's project uses Node.js. He created a container in "Runtime", filled in the project directory, startup command, and environment variables. A few minutes later, the backend API was running. He visited `http://serverIP:3001/api/health` in his browser and saw the JSON response.

### Step 3: Build and Deploy the Frontend

The frontend typically needs to be built locally first, then uploaded to the server:

```bash
# On your local machine
cd your-project

# Set environment variable (pointing to server backend API)
echo "VITE_API_URL=http://your-server-ip:3001" > .env.production

# Build frontend
npm run build  # or pnpm build / yarn build

# Build output is usually in dist/ or build/ or out/ directory
```

Upload to server:

```bash
# Method 1: Using scp
scp -r dist/* root@your-server-ip:/opt/your-frontend/

# Method 2: Using FinalShell drag-and-drop upload
```

Create a static website in 1Panel:

1. Go to "Website > Websites", click "Create Website"
2. Select "Static Website"
3. Primary Domain: `yourdomain.com` (or use IP for testing first)
4. Website Directory: `/opt/your-frontend`
5. Click Confirm

Xiaoming used FinalShell to drag the built frontend files to the server, then created a static website in 1Panel. Visiting the server IP in his browser showed the frontend page—but when he tried to log in, he got an error: "Cross-origin request blocked."

### Step 4: Configure Reverse Proxy (Critical Step)

Now the frontend and backend are separate—the frontend on port 80, the backend on port 3001. API requests from the frontend will encounter CORS issues. The solution is to configure a reverse proxy so that frontend and backend are under the same domain.

#### Recommended Workflow: Let Claude Code Write the Configuration for You

1. **Get 1Panel's Default Configuration**

In 1Panel, go to the settings page of the static website you just created, click the "Configuration File" tab, and copy the entire configuration content.

2. **Use Claude Code Locally to Refine the Configuration**

Open Claude Code locally and send the following prompt:

```
I need to configure an OpenResty reverse proxy for frontend-backend separated deployment.

Current 1Panel default configuration:
[paste your copied configuration]

My requirements:
- Frontend static files are in /opt/your-frontend
- Backend API container name is your-backend, port 3000
- All /api/* requests should be forwarded to the backend
- Other requests should return the frontend page (SPA routing support)

Please help me refine this configuration.
```

Claude Code will generate a complete configuration based on your actual situation, including:
- Correct `proxy_pass` addresses
- Necessary header settings
- Timeout configurations
- SPA routing support (`try_files`)

3. **Copy Back to Server**

Copy the configuration generated by Claude Code back to 1Panel's "Configuration File" edit box, and click "Save and Reload".

Xiaoming copied 1Panel's default configuration to Claude Code locally and explained his requirements. Claude Code quickly generated a complete configuration, including reverse proxy rules, header settings, and timeout configurations. Xiaoming copied it back to 1Panel, clicked save—refreshed the browser, and this time the login succeeded!

::: tip Why Recommend This Workflow?

1. **Claude Code understands your project**: It knows your container names, ports, and routing structure
2. **Avoid manual errors**: Container name typos and path configuration errors are common issues
3. **Auto-completes details**: Timeout settings, headers, error handling, and other easily overlooked configurations
4. **Can iterate repeatedly**: If the configuration isn't right, you can continue to have Claude Code adjust it

:::

::: warning What if Claude Code's Configuration Has Issues?

1. **Check error logs**: View error logs in "App Store → Installed → OpenResty"
2. **Send error info to Claude Code**: Copy the error logs and tell Claude Code "My reverse proxy configuration has issues, the error logs are..."
3. **Claude Code will help you fix it**: It will analyze the error cause and generate a corrected configuration
4. **Common errors**:
   - Container name typo → Check if the container name is correct
   - Wrong port number → Check the actual port of the backend service
   - Path configuration error → Check if the `proxy_pass` path is correct

If multiple attempts still fail, you can refer to the "Typical Reverse Proxy Configuration Example" at the end of this section to manually modify.
:::

#### Typical Reverse Proxy Configuration Example

Configurations generated by Claude Code usually look like this:

```nginx
server {
    listen 80;
    server_name yourdomain.com;

    # Frontend static files
    location / {
        root /opt/your-frontend;
        try_files $uri $uri/ /index.html;  # SPA routing support
    }

    # Backend API reverse proxy
    location /api/ {
        proxy_pass http://your-backend:3000/api/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Timeout settings
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    }
}
```

### Step 5: Update Frontend API Address

Once the reverse proxy is configured, the frontend API address should be changed to a relative path (since frontend and backend are now under the same domain):

```bash
# On your local machine
echo "VITE_API_URL=/api" > .env.production  # or NEXT_PUBLIC_API_URL=/api

# Rebuild
npm run build

# Re-upload to server
scp -r dist/* root@your-server-ip:/opt/your-frontend/
```

Xiaoming rebuilt and re-uploaded the frontend. Refreshed the browser, all functions worked normally—"Frontend, backend, and database, three parts working together on my own server."

## Differences Across Tech Stacks

Although the deployment workflow is similar, different tech stacks have some detailed differences:

### Node.js / Next.js

- **Runtime Language**: Node.js
- **Version Selection**: Choose a Node.js version consistent with your local environment (latest version 24.10.0)
- **Startup Command**: `pnpm build && pnpm start`
- **Port**: Usually 3000
- **Environment Variables**: `DATABASE_URL`, `NODE_ENV=production`

### Python / FastAPI

- **Runtime Language**: Python
- **Version Selection**: Choose a Python version consistent with your local environment (latest version 3.14.0)
- **Startup Command**: `pip install -r requirements.txt && uvicorn main:app --host 0.0.0.0 --port 8000`
- **Port**: Usually 8000
- **Environment Variables**: `DATABASE_URL`, `PYTHONUNBUFFERED=1`

### PHP / Laravel

- **Runtime Language**: PHP
- **Version Selection**: Choose a PHP version consistent with project requirements (latest version 8.5.2)
- **Deployment Method**: PHP runtime provides PHP-FPM service (default port 9000), needs to be used with OpenResty
- **Startup Command**: PHP-FPM auto-starts, no startup command configuration needed
- **Environment Variables**: `DB_CONNECTION`, `DB_HOST` (use container name), `APP_ENV=production`
- **Note**: PHP projects are typically accessed through OpenResty reverse proxy to PHP-FPM, rather than running an independent HTTP server

### Java / Spring Boot

- **Runtime Language**: Java
- **Version Selection**: Choose a JDK version consistent with your project (latest version 22)
- **Startup Command**: `java -jar app.jar`
- **Port**: Usually 8080
- **Environment Variables**: `SPRING_DATASOURCE_URL`, `SPRING_PROFILES_ACTIVE=prod`

### Go

- **Runtime Language**: Go
- **Version Selection**: Choose a Go version consistent with your project (latest version 1.25)
- **Startup Command**: `./app`
- **Port**: Custom (e.g., 8080)
- **Environment Variables**: Custom based on project

### .NET / ASP.NET Core

- **Runtime Language**: .NET
- **Version Selection**: Choose a .NET version consistent with your project (latest version 10.0)
- **Startup Command**: `dotnet run --urls http://0.0.0.0:5000`
- **Port**: Usually 5000
- **Environment Variables**: `ConnectionStrings__DefaultConnection`, `ASPNETCORE_ENVIRONMENT=Production`

## Testing the Complete Workflow

After deployment, test the following functions to ensure everything works:

1. **Visit homepage**: `http://your-server-ip` → See the frontend page
2. **API requests**: API requests initiated by the frontend return data normally
3. **Data persistence**: Data still exists after page refresh
4. **User authentication**: Login, logout, permission verification functions work normally

Xiaoming tested all these functions, all normal. He opened the developer tools Network panel and saw the frontend page was loaded from `/`, and API requests were sent to `/api/xxx`—"Users completely don't feel that three services are running behind the scenes."

## Common Issues Troubleshooting

| Symptom | Possible Cause | Solution |
|------|---------|---------|
| Frontend page won't open | Security group hasn't opened port 80 | Go to cloud provider console to open port 80 |
| API request 404 | Reverse proxy configuration error | Check `proxy_pass` container name and path |
| API request 502 | Backend container not started | Check logs in "Runtime", restart container |
| Database connection failed | `DATABASE_URL` hostname incorrect | Use container name (e.g., `postgresql`) instead of `localhost` |
| CORS error | Reverse proxy not taking effect | Confirm OpenResty configuration has been saved and reloaded |
| SPA routing 404 | Missing `try_files` configuration | Have Claude Code add SPA routing support |

::: tip Debugging Workflow When Encountering Issues

1. **Check logs first**: View logs in "Containers" or "Runtime", 90% of issues have answers in the logs
2. **Check container status**: Confirm all containers are running
3. **Test inter-container communication**: Enter backend container terminal, `ping postgresql` to test network connectivity
4. **Ask Claude Code for help**: Send error logs to Claude Code and let it help you analyze

:::

## Architecture Benefits Summary

Through frontend-backend separated deployment, you gain:

1. **Unified entry**: Users only need to remember one domain, don't need to know which port the backend is on
2. **Avoids CORS**: Frontend and API are under the same domain, no CORS issues
3. **Security**: Backend services aren't directly exposed to the public internet, only OpenResty faces external traffic
4. **Flexible scaling**: Frontend, backend, and database can be upgraded and scaled independently
5. **Complete control**: You have full control over every layer, not limited by platforms

Xiaoming recalled deploying on Vercel, where one `vercel deploy` handled everything. Now although there are more steps, he understands what each step does—"The things Vercel did for me, I can now do myself. And with Claude Code helping write configurations, it's not that hard."

---

::: info Next Step
Application deployment is complete, but now it can only be accessed via IP, which isn't professional. Next, configure domain and HTTPS—[14.4 Configuring Domain and SSL](./04-domain-ssl.md).
:::