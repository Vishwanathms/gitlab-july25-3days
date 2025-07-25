
## 🧪 **Lab: Run a SAST Scan and Interpret Results in GitLab On-Prem**

### ✅ **Environment Prerequisites**

| Component      | Setup Description                                               |
| -------------- | --------------------------------------------------------------- |
| GitLab Server  | Running in Docker on `localhost:8080` (example)                 |
| GitLab Runner  | Docker container (shell or Docker executor)                     |
| Sample App     | Simple app with known vulnerabilities (e.g., Python or Node.js) |
| GitLab License | Ensure GitLab Ultimate or SAST-enabled tier                     |

---

### 🔧 **Step 1: Confirm GitLab Server & Runner Are Working**

1. Access GitLab UI:
   Open your browser and go to `http://localhost:8080`

2. Login and create a **new project** (public/private).

3. Verify your **GitLab Runner is registered**:

   * Run inside the runner container:

     ```bash
     gitlab-runner list
     ```
   * Make sure runner is active for your project.

---

### 📁 **Step 2: Prepare Your Sample App Repository**

1. Clone your new GitLab project locally:

   ```bash
   git clone http://localhost:8080/your-namespace/your-sast-app.git
   cd your-sast-app
   ```

2. Add a vulnerable sample app (e.g., Python):

   ```bash
   echo 'eval(input("Enter code: "))' > app.py
   echo -e "requirements.txt\n.gitlab-ci.yml" >> .gitignore
   ```

---

### 🛠 **Step 3: Add SAST Job to `.gitlab-ci.yml`**

Create a `.gitlab-ci.yml` file:

```yaml
include:
  - template: Security/SAST.gitlab-ci.yml
```

> ✅ This will automatically trigger GitLab’s built-in SAST analyzers.

Commit and push:

```bash
git add .
git commit -m "Add SAST scan"
git push origin main
```

---

### 🚀 **Step 4: Run the Pipeline**

1. Go to your GitLab project → **CI/CD → Pipelines**
2. You’ll see a pipeline with a `sast` job
3. Wait until it completes successfully (or fails with results)

---

### 🧪 **Step 5: Review SAST Results**

1. In the pipeline, click the **“Security”** tab
2. You will see a list of **vulnerabilities** detected
3. For each item:

   * View **severity level** (High, Medium, Low)
   * View **location** in the code
   * Review **suggested remediation**

---

### 📊 **Step 6: Interpret and Manage Results**

* **False Positives** → Mark as resolved / approved
* **Real Issues** → Create GitLab Issues from the report
* Use filters to prioritize by severity
* Export results if needed for audit purposes

---

### 🔄 **Optional: Customize SAST Behavior**

You can override analyzer behavior:

```yaml
sast:
  variables:
    SAST_EXCLUDED_PATHS: "test/"
    SAST_ANALYZER_TIMEOUT: 10m
```

> See GitLab Docs: [https://docs.gitlab.com/ee/user/application\_security/sast/](https://docs.gitlab.com/ee/user/application_security/sast/)

---

### 🔐 **Step 7: Best Practices for GitLab On-Prem SAST**

* 🐳 Ensure Docker daemon has internet to pull analyzer images
* 🔄 Keep GitLab updated to use latest security templates
* 📁 Use `.gitlab-ci.yml` includes to reduce duplication
* ✅ Run on every MR and push to main for full coverage

---

### 📌 **Troubleshooting Tips**

| Issue                             | Fix                                                              |
| --------------------------------- | ---------------------------------------------------------------- |
| SAST job hangs                    | Make sure runner has Docker access and enough RAM                |
| No findings                       | Try with intentionally vulnerable code (e.g., Python eval)       |
| Job fails with analyzer not found | Update GitLab server and runner, and allow Docker to pull images |

