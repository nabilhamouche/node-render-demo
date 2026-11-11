# Lab 04 — Deploy a Web Application on a PaaS Platform (Render)

## Deliverables

- **GitHub repository:** https://github.com/nabilhamouche/node-render-demo
- **Live Render URL:** https://node-render-demo-ul7a.onrender.com
- **Evidence:**
  - Screenshot 1: `npm start` in the terminal + `http://localhost:3000` showing "Hello from Render!"
  - Screenshot 2: Render dashboard showing the deploy as **Live**
  - Screenshot 3: the Render URL in the browser showing "Hello from Render!"
  - Screenshot 4: the updated message on the Render URL after the automatic redeploy

## Questions

### 1. What tasks does Render manage that you would otherwise need to manage on a virtual machine?
On a VM I would have to do all of this myself. Render does it for me:
- Provisioning and maintaining the server and operating system (OS updates, security patches).
- Installing and updating the Node.js runtime.
- Fetching the code, running the build (`npm install`) and starting the app (`npm start`).
- Networking: giving the app a public URL, DNS, HTTPS/TLS certificates and a reverse proxy/load balancer.
- Restarting the app when it crashes and running health checks.
- Redeploying automatically when new code is pushed (CI/CD).
- Collecting logs and showing metrics in a dashboard.
- Scaling (more instances or bigger instances) without configuring servers by hand.

### 2. What are the roles of Node.js and Express in this application?
- **Node.js** is the JavaScript runtime. It runs `index.js` on the server, outside the browser, and its event loop handles the incoming network connections.
- **Express** is a web framework that runs on Node.js. It makes it easy to define HTTP routes (here `GET /`), send responses (`res.send(...)`) and start the HTTP server (`app.listen`).

### 3. Why does the code use `process.env.PORT` instead of relying only on port 3000?
On a PaaS the platform decides which port the app must listen on and passes it in the `PORT` environment variable. Render's router forwards public traffic to that port. If the app always used 3000, it might not match what Render expects, and the deploy would fail or the app would be unreachable. `|| 3000` is only a fallback for local development, where `PORT` isn't set. Listening on `0.0.0.0` (all network interfaces) also lets the platform's router reach the app from outside the container.

### 4. What role does GitHub play in this deployment workflow?
GitHub hosts the source code under version control and is the **source of truth** for deployments. Render is connected to the repository: it clones the code to build it, and every push to the `main` branch automatically triggers a new build and deploy. So `git push` is the deployment trigger (continuous deployment). It also keeps the history of changes so a previous version can be rolled back to.

### 5. Why is this an example of PaaS?
I only provided the **application code** plus two commands (build: `npm install`, start: `npm start`). Render provided and managed everything underneath: servers, OS, the Node.js runtime, networking, HTTPS, the public URL, deployment, restarts and logs. I never created or logged into a server. In IaaS (a VM) I would manage the OS and runtime myself. In SaaS I would only use a finished application. PaaS sits between the two: **I manage the app, the provider manages the platform.**
