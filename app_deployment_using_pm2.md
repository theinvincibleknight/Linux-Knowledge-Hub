## Step-by-step documentation to set up, deploy, run, and monitor a simple application using PM2 (Process Manager 2) on Ubuntu:

### Prerequisites

1. An Ubuntu system with internet access
2. Node.js and npm installed (you can install them with `sudo apt-get install nodejs npm`)

### 1. Create a Simple Node.js Application

1. Create a new directory for your application:
```bash
mkdir my-app
cd my-app
```
2. Initialize a new Node.js project:
```bash
npm init -y
```
3. Create a new file `app.js` with the following content:
```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello, World!\n');
});

const port = process.env.PORT || 3000;
server.listen(port, () => {
  console.log(`Server running at http://localhost:${port}/`);
});
```
This code creates a simple HTTP server that listens on port 3000 and responds with "Hello, World!" when accessed.

### 2. Install PM2 Globally
```bash
sudo npm install -g pm2
```
PM2 is a process manager for Node.js applications that keeps your applications running, handles log management, and provides other useful features.

### 3. Deploy the Application Using PM2

1. Start the application with PM2:
```bash
pm2 start app.js
```
This command will start your application and automatically assign it an ID.

2. Check the status of the running application:
```bash
pm2 status
```
You should see your application listed with its ID and other details.

3. (Optional) Give your application a name for easier management:
```bash
pm2 restart app.js --name "my-app"
```

### 4. Configure PM2 to Start on System Startup
To ensure that your application starts automatically when the system boots up, you need to configure PM2 to run as a system service:
```bash
pm2 startup
```
This command will provide instructions specific to your system. Follow the instructions to set up PM2 as a service.

### 5. Access the Application
You can now access your application by visiting `http://localhost:3000` in your web browser or using `curl` from the command line:
```bash
curl http://localhost:3000
```
You should see the "Hello, World!" response.

### 6. Monitor Logs
PM2 provides a way to monitor your application's logs. You can view the logs with:
```bash
pm2 logs
```
This command will display the logs in real-time. You can also view the logs for a specific application by using its name or ID:
```bash
pm2 logs my-app
```

### 7. Additional PM2 Commands
Here are some additional useful PM2 commands:

- `pm2 stop <app_name|id>`: Stop a running application
- `pm2 restart <app_name|id>`: Restart a running application
- `pm2 delete <app_name|id>`: Delete an application from PM2
- `pm2 list`: List all running applications
- `pm2 monit`: Monitor all running applications (CPU, memory usage, etc.)

You've successfully set up, deployed, and monitored a simple Node.js application using PM2 on Ubuntu. You can now explore more advanced features of PM2, such as clustering, load balancing, and deployment workflows.
