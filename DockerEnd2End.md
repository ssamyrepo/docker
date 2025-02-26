## **Step-by-Step Guide to Docker Containers, Databases, Compose & Beyond**


# **🔹  1: Setting Up**
Setup GITHub and Docker account
---

# **🔹  2: Installing Docker**
### **2️⃣ Install Docker & Sign in to Docker Hub**
1. **Go to Docker's official website**:  
   [https://docs.docker.com/get-docker/](https://docs.docker.com/get-docker/)
2. Download and install **Docker Desktop** for your OS.
   - Windows: `.exe`
   - Mac: `.dmg`
   - Linux: Use package manager.
3. Open **Docker Desktop** and **Sign in** to your **Docker Hub** account.

---

# **🔹  3: Running a Python App in a Container**
### **3️⃣ Pull the Python Image & Run a Python Script**
1. **Open Terminal** (Linux/Mac) or **PowerShell** (Windows).
2. Pull the Python 3.8 image:
   ```sh
   docker pull python:3.8-slim
   ```
3. Verify the downloaded image:
   ```sh
   docker images
   ```
4. Run a Python script inside a Docker container:
   ```sh
   docker run -v "$(pwd):/src" -w /src python:3.8-slim python script.py
   ```
   - `-v "$(pwd):/src"` → Mounts current directory to `/src` inside the container.
   - `-w /src` → Sets the working directory inside the container.
   - `python:3.8-slim` → Uses the lightweight Python image.
   - `python script.py` → Runs your script.

5. **Clean Up:**
   - Remove stopped containers:
     ```sh
     docker ps -a
     docker rm <CONTAINER_ID>
     ```
   - Remove images:
     ```sh
     docker rmi python:3.8-slim
     ```

---

# **🔹  4: Using a Dockerfile**
### **4️⃣ Create a Custom Docker Image**
1. Inside your project folder, create a `Dockerfile`:
   ```dockerfile
   FROM python:3.8-slim
   WORKDIR /src
   COPY . .
   CMD ["python", "script.py"]
   ```
2. Build the Docker image:
   ```sh
   docker build -t my-python-app .
   ```
3. Run the container:
   ```sh
   docker run my-python-app
   ```

---

# **🔹  5: Running a Flask Web App**
### **5️⃣ Build & Run a Flask App in a Container**
1. **Create a Flask app** (`app.py`):
   ```python
   from flask import Flask
   app = Flask(__name__)

   @app.route('/')
   def home():
       return "Hello from Flask inside Docker!"

   if __name__ == '__main__':
       app.run(host='0.0.0.0', port=5000)
   ```
2. **Create a `requirements.txt` file**:
   ```
   flask
   ```
3. **Write a Dockerfile**:
   ```dockerfile
   FROM python:3.8-slim
   WORKDIR /app
   COPY . .
   RUN pip install -r requirements.txt
   EXPOSE 5000
   CMD ["python", "app.py"]
   ```
4. **Build & Run**:
   ```sh
   docker build -t flask-app .
   docker run -p 5000:5000 flask-app
   ```
5. **Test in a Browser:**  
   Open [http://localhost:5000](http://localhost:5000).

---

# **🔹  6: Pushing an Image to Docker Hub**
### **6️⃣ Publish Your Image**
1. **Log in to Docker Hub**:
   ```sh
   docker login
   ```
2. **Tag your image**:
   ```sh
   docker tag flask-app <your-dockerhub-username>/flask-app:v1
   ```
3. **Push the image**:
   ```sh
   docker push <your-dockerhub-username>/flask-app:v1
   ```
4. **Pull & Run on another machine**:
   ```sh
   docker pull <your-dockerhub-username>/flask-app:v1
   docker run -p 5000:5000 <your-dockerhub-username>/flask-app:v1
   ```

---

# **🔹  7: Using Environment Variables**
### **7️⃣ Secure Credentials with Env Variables**
1. **Modify `app.py`**:
   ```python
   import os
   from flask import Flask

   app = Flask(__name__)
   DB_USER = os.getenv('DB_USER', 'default_user')

   @app.route('/')
   def home():
       return f"Connected as {DB_USER}"

   if __name__ == '__main__':
       app.run(host='0.0.0.0', port=5000)
   ```
2. **Run with Environment Variables**:
   ```sh
   docker run -p 5000:5000 -e DB_USER=myuser flask-app
   ```

---

# **🔹  8: Running MySQL in Docker**
### **8️⃣ Create & Connect to MySQL**
1. **Pull MySQL image**:
   ```sh
   docker pull mysql:8
   ```
2. **Run MySQL in a container**:
   ```sh
   docker run -d --name mysql-container -e MYSQL_ROOT_PASSWORD=rootpass -e MYSQL_DATABASE=mydb -p 3306:3306 mysql:8
   ```
3. **Connect using MySQL CLI**:
   ```sh
   docker exec -it mysql-container mysql -uroot -prootpass
   ```

---

# **🔹  9: Multi-Container Setup with Docker Compose**
### **9️⃣ Define `docker-compose.yml`**
1. **Create `docker-compose.yml`**:
   ```yaml
   version: '3.8'

   services:
     db:
       image: mysql:8
       environment:
         MYSQL_ROOT_PASSWORD: rootpass
         MYSQL_DATABASE: mydb
       ports:
         - "3306:3306"
     app:
       build: .
       ports:
         - "5000:5000"
       depends_on:
         - db
   ```
2. **Start both containers**:
   ```sh
   docker-compose up -d
   ```
3. **Stop & remove containers**:
   ```sh
   docker-compose down
   ```

---

# **🔹  10: Persistent Data with Volumes**
### **🔟 Add Persistent Storage**
1. **Modify `docker-compose.yml`**:
   ```yaml
   services:
     db:
       volumes:
         - mysql-data:/var/lib/mysql
   volumes:
     mysql-data:
   ```
2. **Run MySQL with persistent storage**:
   ```sh
   docker-compose up -d
   ```
3. **Verify data persistence**:
   - Stop containers: `docker-compose down`
   - Restart: `docker-compose up -d`
   - Check if MySQL data is retained.

---
