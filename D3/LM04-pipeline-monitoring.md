Here is a detailed **step-by-step lab manual** for:

🧪 **Hands-On Lab: Setting up Pipeline Failure Notification and Displaying Build Badges**
**Environment:**

* GitLab server running in a Docker container
* GitLab runner in a Docker container
* You have access to project-level GitLab UI

---

## ✅ **Objective**

1. Configure **failure notifications** (via email or Slack) in GitLab.
2. Add a **build status badge** to your `README.md` for visibility.

---

## 🔧 **Lab Setup Pre-requisites**

* GitLab CE/EE installed on `http://localhost:8080`
* GitLab Runner registered for your project
* SMTP server configured (for email alerts) — optional if only Slack is used
* Slack workspace with a Webhook URL — if Slack is preferred
* A sample pipeline with `.gitlab-ci.yml`

---

## 🔹 **PART 1: Setup Notification on Pipeline Failure**

---

### 🔸 Step 1: Configure Slack Notifications (Recommended)

1. Go to your **Slack Workspace**

   * Search: *Incoming Webhooks app*
   * Add a new incoming webhook to a Slack channel (e.g., `#ci-notify`)
   * Copy the **Webhook URL**

2. In GitLab:

   * Go to your project → **Settings > Integrations**
   * Choose **Slack notifications**
   * Paste the **Webhook URL**
   * Select events: ✅ *Pipeline Failed*, ✅ *Job Failed*, ✅ *Manual Actions*
   * Click **Test** to verify

---

### 🔸 Step 2: Configure Email Notifications (Optional)

GitLab sends **default email alerts** to:

* The user who triggered the pipeline
* Any users watching the project with **notification level: Watch**

To adjust email notifications:

* Go to **User Profile > Notifications**
* Set level to `Watch` or `Custom`
* Ensure **SMTP is configured** in `gitlab.rb` if running self-hosted GitLab

---

## 🔹 **PART 2: Trigger and Observe Failure Notification**

1. Create a broken pipeline intentionally:

```yaml
# .gitlab-ci.yml
stages:
  - test

fail-job:
  stage: test
  script:
    - exit 1
```

2. Commit and push:

```bash
git add .
git commit -m "Trigger failure"
git push origin main
```

3. Observe:

   * Slack channel should receive a failure notification
   * Email (if configured) sent to project watchers

---

## 🔹 **PART 3: Add Pipeline Build Badge in README**

---

### 🔸 Step 1: Locate Badge Link in GitLab

1. Go to your **Project → Settings → CI/CD**
2. Scroll down to **General pipelines settings → Pipeline status**
3. Copy the **Markdown badge** snippet (e.g.):

```markdown
[![pipeline status](http://localhost:8080/your-group/your-project/badges/main/pipeline.svg)](http://localhost:8080/your-group/your-project/-/pipelines)
```

---

### 🔸 Step 2: Add to Your README

1. Open your `README.md`
2. Paste the badge code at the top:

```markdown
# My GitLab CI Project

[![pipeline status](http://localhost:8080/your-group/your-project/badges/main/pipeline.svg)](http://localhost:8080/your-group/your-project/-/pipelines)

This is a demo repo for GitLab CI/CD.
```

3. Commit and push:

```bash
git add README.md
git commit -m "Add build status badge"
git push origin main
```

4. Go to your project homepage and confirm the badge appears correctly.

---

## ✅ **Expected Outcome**

| Task                     | Result                                |
| ------------------------ | ------------------------------------- |
| Failing pipeline pushed  | Slack/email notification is triggered |
| Badge added to README.md | Pipeline build status shown publicly  |

---

## 🧠 **Tips**

* You can use GitLab Webhooks for advanced integrations (PagerDuty, MS Teams, etc.)
* Protect badge URLs using private tokens if project is private
* Add job-level `after_script:` to include extra logging or alerts

