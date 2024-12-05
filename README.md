# Docker Compose for a Multi Service Application

## Scenario: 
Create a docker-compose.yml file to run a Node.js application (app.js) and a MongoDB database together.

## Requirements:
1.	The Node.js application should connect to MongoDB on a service named db.
2.	The MongoDB container should use the official image and expose port 27017.
3.	The Node.js application should restart automatically if it fails.

## Expected Deliverables:
4.	A docker-compose.yml file.
5.	Steps to start the services and verify they are running (docker-compose up).
6.	A brief explanation of service dependencies in the docker-compose file.

---
### **4. A docker-compose.yml file.**

 [docker-compose.yml](./docker-compose.yml)

---
### **5. Steps to Start the Services and Verify They Are Running**

1. **Build and Start the Containers**
   - Use the following command to build the Node.js application and start all services:
     ```bash
     docker-compose up --build -d
     ```

2. **Verify Running Containers**
   - Check the status of running containers:
     ```bash
     docker ps
     ```
   - Ensure both `node-app` and `mongo-db` are running.

3. **Verify Connectivity**
   - Access the Node.js application in your browser or via `curl`:
     ```bash
     curl http://<host_ip>:3000
     ```
  
---

### **6. Explanation of Service Dependencies**

- **`db` (MongoDB Service)**:
  - Runs the MongoDB database using the official `mongo:latest` image.
  - Exposes port `27017` to allow database connectivity.
  - Uses the `app-network` for internal communication.

- **`node-app` (Node.js Service)**:
  - The `node-app` service builds the Node.js application from the local directory using the specified `Dockerfile`.
  - Exposes port `3000` for external access to the application.
  - Sets the `MONGO_URI` environment variable to connect to the `db` service using the internal service name (`db`), ensuring the application connects seamlessly via the `app-network`.

- **`restart: on-failure`**:
  - Restart the container if it exits due to an error, which manifests as a non-zero exit code. The on-failure policy only prompts a restart if the container exits with a failure. It doesn't restart the container if the daemon restarts.

- **Dependencies (`depends_on`)**:
  - The `depends_on` directive ensures that the `db` service starts before `node-app`. This helps in maintaining service order during startup, but it does not guarantee readiness of the `db` service (e.g., MongoDB being ready to accept connections). However, the application gracefully handles reconnection attempts with `mongoose`.

- **Network (`app-network`)**:
  - Both services are attached to a custom `bridge` network named `app-network`. This ensures secure and isolated communication between the services using their service names as hostnames.
