# Archon Application Starter Template

Welcome to the **Archon Golden Path**.
This repository is the **foundational template** for all new unity microservices at **Archon-Programmatic-House-SW**.

By using this template, your service **automatically inherits a secure, standardized, production-grade CI/CD pipeline** — without ever writing complicated infrastructure code.

## Getting Started

### 1️⃣ Create Your Repository

1. Click **Use this template → Create a new repository (select organization not personal account)**.
2. Name your repo (e.g., `celebrity-api`, `car-api`).

### 2️⃣ Configure Your Service

Open `.github/workflows/` and edit:

---

## A. Development Pipeline (`dev-pipeline.yml`)

**Trigger:** Pushes to the `develop` branch.

Update:

1. `service_name: test-service` → change to **your service name**.
2. `runtime_service_account` → set the SA email your service uses at runtime.
3. Uncomment and fill `env_vars` if your service requires environment variables.
4. Add your testing commands inside the `test-and-verify` job:

   * `npm test`
   * `pytest`
   * `dotnet test`
   * etc.

---

## B. Production Release (`prod-release.yml`)

**Trigger:** A tag push (e.g., `v1.0.1`).

Update:

* `service_name`
* `project_id` (archonunity-prod)
* `runtime_service_account` (Prod services use dedicated identities)

---

# 🔄 Workflow Overview

This repository includes **three workflows** that work together in a full CI/CD lifecycle.

---

## 1. Development Pipeline (`dev-pipeline.yml`)

**Runs when:** You push code to the `develop` branch.

### What it does:

* **Test & Verify:** Runs unit tests.

  * If tests fail → deployment stops.
* **Deploy to Dev:**

  * Calls the DevOps central repo (`devops-templates`)
  * Builds your Docker image
  * Pushes it to Artifact Registry
  * Deploys to Cloud Run in the **Development environment**

---

## 2. Create Release Tag (`create-release-tag.yml`)

**Runs when:** Manually triggered from the **Actions** tab.

### What it does:

* You choose the release type → **patch / minor / major**
* Calculates the next semantic version
* Creates the git tag (e.g., `v1.2.0`)
* Pushes the tag using the organization token `GH_PAT_TAG` (Right now created with my own account will be updated to organization bot account)

The **pushed tag automatically triggers the Production Release workflow**.

---

## 3. Production Release (`prod-release.yml`)

**Runs when:** Any tag starting with `v*` is pushed. ( When manually create-release-tag.yml workflow triggered)

### What it does:

* **Verify Release:** Optional checks (changelog validation, integrity checks).
* **Deploy to Prod:**

  * Calls the central DevOps deployment pipeline
  * Deploys the exact tagged code to **Production Cloud Run**

---

# Secrets & Permissions

This template relies on **Organization-Level Secrets** managed by DevOps:

| Secret Name            | Level | Description                                                                                                     |
| ---------------------- | ----- | -------------------------------------------------                                                               |
| `ORG_WIF_PROVIDER`     | Org   | Connects GitHub to GCP via WIF (no keys).                                                                       |
| `ORG_CI_BUILDER_EMAIL` | Org   | Service Account used for CI/CD build & deploy.                                                                  |
| `GH_PAT_TAG`           | Org   | Token used to push version tags and trigger Prod. |

> You normally **don’t need repo-level secrets**, If needed add secrets to your repo. Repo level secrets will override organizational level secrets.
> Organizaton secrets belong to archonunity-staging right now will be updated later

---

