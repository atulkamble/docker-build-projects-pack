# Docker Build Projects Pack — Cloudnautic

A ready‑to‑push set of project blueprints with repo names, minimal code, Dockerfiles, and run commands. Copy any section into a new GitHub repo and go! 👇

---

## 1) docker-hello-world (Nginx static site)

**Tree**

```
docker-hello-world/
├─ Dockerfile
└─ html/
   └─ index.html
```

**Dockerfile**

```dockerfile
FROM nginx:alpine
COPY html /usr/share/nginx/html
EXPOSE 80
```

**html/index.html**

```html
<!doctype html>
<html>
  <head><meta charset="utf-8"><title>Hello from Docker</title></head>
  <body style="font-family: system-ui; text-align:center; margin-top:10vh;">
    <h1>🚀 Hello, Cloudnautic!</h1>
    <p>Nginx static site running inside Docker.</p>
  </body>
</html>
```

**Build & Run**

```bash
docker build -t atuljkamble/docker-hello-world:latest .
docker run -d -p 80:80 --name hello atuljkamble/docker-hello-world:latest
```

---

## 2) docker-flask-app (Python Flask)

**Tree**

```
docker-flask-app/
├─ Dockerfile
├─ requirements.txt
└─ app.py
```

**Dockerfile**

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app.py .
EXPOSE 5000
CMD ["python", "app.py"]
```

**requirements.txt**

```
flask==3.0.3
```

**app.py**

```python
from flask import Flask, jsonify
app = Flask(__name__)

@app.get("/")
def root():
    return jsonify(message="Hello from Flask in Docker 🐳"), 200

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

**Build & Run**

```bash
docker build -t atuljkamble/docker-flask-app:latest .
docker run -d -p 5000:5000 --name flask-app atuljkamble/docker-flask-app:latest
```

---

## 3) docker-node-api (Node.js Express API)

**Tree**

```
docker-node-api/
├─ Dockerfile
├─ package.json
└─ server.js
```

**Dockerfile**

```dockerfile
FROM node:22-alpine
WORKDIR /usr/src/app
COPY package.json .
RUN npm install --only=production
COPY server.js .
EXPOSE 3000
CMD ["node", "server.js"]
```

**package.json**

```json
{
  "name": "docker-node-api",
  "version": "1.0.0",
  "main": "server.js",
  "type": "module",
  "scripts": {"start": "node server.js"},
  "dependencies": {"express": "^4.19.2"}
}
```

**server.js**

```js
import express from "express";
const app = express();
app.get("/", (req, res) => res.json({ message: "Hello from Node API in Docker 🐳" }));
app.listen(3000, () => console.log("API on :3000"));
```

**Build & Run**

```bash
docker build -t atuljkamble/docker-node-api:latest .
docker run -d -p 3000:3000 --name node-api atuljkamble/docker-node-api:latest
```

---

## 4) docker-springboot-app (Java Spring Boot)

**Tree**

```
docker-springboot-app/
├─ Dockerfile
├─ mvnw
├─ mvnw.cmd
├─ .mvn/wrapper/*
├─ pom.xml
└─ src/main/java/com/cloudnautic/demo/DemoApplication.java
```

**Dockerfile** (multi-stage)

```dockerfile
# Build stage
FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /app
COPY pom.xml .
RUN mvn -q -e -B dependency:go-offline
COPY src ./src
RUN mvn -q -e -B package -DskipTests

# Run stage
FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/app/app.jar"]
```

**Build & Run**

```bash
docker build -t atuljkamble/docker-springboot-app:latest .
docker run -d -p 8080:8080 --name spring atuljkamble/docker-springboot-app:latest
```

---

## 5) docker-wordpress-mysql (WordPress + MySQL, Compose)

**docker-compose.yml**

```yaml
services:
  db:
    image: mysql:8.0
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wp
      MYSQL_PASSWORD: wp_pass
      MYSQL_ROOT_PASSWORD: root_pass
    volumes:
      - db_data:/var/lib/mysql
    restart: unless-stopped

  wordpress:
    image: wordpress:php8.2-apache
    ports:
      - "8081:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wp
      WORDPRESS_DB_PASSWORD: wp_pass
      WORDPRESS_DB_NAME: wordpress
    depends_on:
      - db
    restart: unless-stopped

volumes:
  db_data:
```

**Run**

```bash
docker compose up -d
# Open http://localhost:8081
```

---

## 6) docker-react-node-app (React build + Node API)

**docker-compose.yml**

```yaml
services:
  api:
    build: ./api
    ports: ["3000:3000"]
  client:
    build: ./client
    ports: ["8082:80"]
    depends_on: [api]
```

**Run**

```bash
docker compose up -d
# Open http://localhost:8082 and click "Test API"
```

---

## 7) docker-flask-postgres (Flask + PostgreSQL, Compose)

**Run**

```bash
docker compose up -d
# Open http://localhost:5001
```

---

## 8) docker-jenkins-server (Jenkins in Docker)

**Build & Run**

```bash
docker build -t atuljkamble/docker-jenkins-server:latest .
docker run -d -p 8083:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home --name jenkins atuljkamble/docker-jenkins-server:latest
```

---

## 9) docker-prometheus-grafana (Monitoring stack, Compose)

**Run**

```bash
docker compose up -d
# Prometheus: http://localhost:9090, Grafana: http://localhost:3001
```

---

## 10) docker-elk-stack (Elasticsearch + Kibana)

**Run**

```bash
docker compose up -d
# Kibana: http://localhost:5601
```

---

### Push to GitHub

```bash
git init && git add . && git commit -m "init: docker build project"
git branch -M main
git remote add origin https://github.com/atulkamble/<repo-name>.git
git push -u origin main
```
