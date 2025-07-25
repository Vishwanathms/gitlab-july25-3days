
# **Lab Manual: Building & Pushing Docker Images with GitLab CI/CD (Assuming GitLab on Docker)**

---

## **Lab Overview**

* **Goal:** Learn to build Docker images in GitLab CI/CD pipelines and push them to GitLab’s Container Registry.
* **Pre-requisites (Assumptions):**

  * GitLab server is running (in Docker or otherwise).
  * GitLab Runner with Docker executor is running, registered, and can build/push Docker images.
  * You have access to a GitLab project (with Container Registry enabled).
  * Docker and Git are installed locally (optional, for local tests).

---

## **Lab Exercises**

---

### **1. Create or Use an Existing GitLab Project**

1. Log in to GitLab.
2. Use an existing project or create a new blank project (e.g., `docker-ci-demo`).

---

### **2. Prepare a Simple Dockerized Application**

1. **On your local machine:**

   ```sh
   mkdir docker-ci-demo && cd docker-ci-demo
   ```

2. **Create a sample app. Example (Flask app in Python):**

   * `app.py`:

     ```python
     from flask import Flask
     app = Flask(__name__)

     @app.route('/')
     def hello():
         return "Hello, Docker CI with GitLab!"
     ```
   * `requirements.txt`:

     ```
     flask
     ```
   * `Dockerfile`:

     ```dockerfile
     FROM python:3.11-slim
     WORKDIR /app
     COPY requirements.txt .
     RUN pip install -r requirements.txt
     COPY . .
     EXPOSE 5000
     CMD ["python", "app.py"]
     ```

3. **Initialize and push to GitLab:**

   ```sh
   git init
   git remote add origin <YOUR_GITLAB_PROJECT_URL>
   git add .
   git commit -m "Initial commit"
   git push -u origin master
   ```

---

### **3. Verify GitLab Container Registry Availability**

1. In your GitLab project, go to **Packages & Registries > Container Registry**.
2. Note the image path (e.g., `registry.gitlab.com/<namespace>/<project>`).

---

### **4. Configure GitLab CI/CD for Docker Build & Push**

1. **Create a `.gitlab-ci.yml` in your project root:**

   ```yaml
   image: docker:24.0.5

   services:
     - docker:24.0.5-dind

   variables:
     DOCKER_DRIVER: overlay2
     DOCKER_TLS_CERTDIR: ""

   stages:
     - build
     - push

   before_script:
     - docker info

   build-image:
     stage: build
     script:
       - docker build -t $CI_REGISTRY_IMAGE:latest .

   push-image:
     stage: push
     script:
       - echo $CI_REGISTRY_PASSWORD | docker login -u $CI_REGISTRY_USER --password-stdin $CI_REGISTRY
       - docker push $CI_REGISTRY_IMAGE:latest
     only:
       - master
   ```

2. **Commit and push:**

   ```sh
   git add .gitlab-ci.yml
   git commit -m "Add GitLab CI for Docker build and push"
   git push
   ```

---

### **5. Observe the Pipeline Execution**

1. In GitLab, go to **CI/CD > Pipelines**.
2. Monitor the pipeline as it executes the `build` and `push` stages.
3. On success, check **Packages & Registries > Container Registry** to see your new image.

---

### **6. (Optional) Pull and Run Your Docker Image Locally**

1. Log in to the registry from your terminal:

   ```sh
   docker login registry.gitlab.com
   ```

   * Use your GitLab username and a [Personal Access Token](https://gitlab.com/-/profile/personal_access_tokens).

2. Pull the image:

   ```sh
   docker pull registry.gitlab.com/<namespace>/<project>:latest
   ```

3. Run the container:

   ```sh
   docker run -p 5000:5000 registry.gitlab.com/<namespace>/<project>:latest
   ```

4. Visit `http://localhost:5000` and confirm you see:
   **Hello, Docker CI with GitLab!**

---

### **7. (Optional) Clean Up Old Images**

* In GitLab, under **Container Registry**, delete unused tags and images.

---

## **Advanced Practice (Optional)**

* Add automated tests before building images in the pipeline.
* Use commit SHA or branch name in Docker image tags.
* Add a scheduled CI job for cleaning old images.

---

## **Troubleshooting**

* **Pipeline can’t build Docker images?**
  Ensure runner is registered with Docker executor and has correct permissions/mounts.

* **Docker login fails?**
  Use a valid Personal Access Token with appropriate scope.

* **Image not in registry?**
  Check for errors in the pipeline logs under CI/CD > Pipelines.

---

## **Summary**

You now have a streamlined pipeline in GitLab CI/CD that builds Docker images and pushes them to GitLab’s Container Registry.

**Ready for next steps or want to explore advanced workflows (multi-stage builds, automated tests, etc.)? Just ask!**
