# actions-experience 🛠️

This repository demonstrates how to integrate GitHub Actions with [Port](https://www.getport.io) self‑service actions. It provides a workflow that, when triggered by Port, can create or update resources in Port via its API.

---

## 🚀 Getting Started

To use this action with your own Port self-service experience, **you must clone this repository into your own GitHub organization**:

```bash
git clone https://github.com/port-experimental/actions-experience.git
cd actions-experience
# Optionally make it private or rename as needed
```

> Port’s self-service actions expect to trigger a workflow in **your own GitHub repo**, so make sure this workflow file lives in your organization’s GitHub repository and is correctly referenced in the Port action definition.

---

## 🎯 Purpose

The GitHub Actions workflow in this repo is designed to be triggered by a Port self-service action. Once triggered, it:

- Accepts parameters from the Port action.
- Performs your custom logic (e.g., provisioning resources).
- Uses Port’s API to log progress and update the status of the action run.

This allows developers to perform infrastructure tasks using a no-code UI in Port, backed by automation defined in GitHub.

---

## ⚙️ Required Environment Variables

Set these secrets in your GitHub repository under **Settings → Secrets and variables → Actions**:

| Variable             | Description                                                              |
|----------------------|--------------------------------------------------------------------------|
| `PORT_CLIENT_ID`     | Client ID from Port (from the **Credentials** section in the Port app)   |
| `PORT_CLIENT_SECRET` | Client Secret associated with the above client ID                        |

> These credentials are used to authenticate to the Port API using the [client credentials flow](https://docs.port.io/api-reference/port-api/#tag/Auth/operation/login).

---

## 🧩 How it Works

### 1. Port Self-Service Action

- You define a self-service action in Port with an invocation method of type `GITHUB`, pointing to the cloned repo and workflow file.
- You configure inputs, guardrails, and triggers in Port.

### 2. Workflow Invocation

- When a user runs the self-service action in Port, it triggers the GitHub Actions workflow via API and passes required parameters (e.g., `run_id`, `entity`, etc.) as inputs.

### 3. Action Logic Execution

- The workflow uses the client credentials to obtain an auth token.
- It logs messages and updates the action run using the `run_id` passed automatically in the workflow inputs.

### 4. Port Reflects Changes

- Port displays logs and status updates in the UI.
- Entities in the software catalog can be created or updated based on the workflow logic.

---

## 📄 File Overview

- `.github/workflows/port-action.yml`  
  Defines inputs, authenticates to Port, and interacts with the API.

---

## ✅ Setup Instructions

1. Clone this repository into your GitHub organization.
2. Add the following required secrets under your repo’s GitHub Actions settings:
   - `PORT_CLIENT_ID`
   - `PORT_CLIENT_SECRET`
3. Define the self-service action in Port:
   - Go to **Self-service → Create Action**
   - Choose the `GITHUB` invocation type and point to your workflow file
   - Add any inputs required by your workflow (e.g., environment, team, service)
   - Set guardrails and access controls if needed
4. Test the action by triggering it from the Port UI.

---

## 📡 Reporting Logs and Status to Port

The GitHub Action uses the `run_id` passed by Port to:

### Send logs

```http
POST /v1/actions/runs/${run_id}/logs
```

### Mark run status (success/failure)

```http
PATCH /v1/actions/runs/${run_id}
```

With body:

```json
{
  "status": "SUCCESS",
  "output": {
    "message": "Successfully created resource",
    "external_url": "https://your-ci-system.com/job/1234"
  }
}
```

➡️ See [Port API Reference](https://docs.port.io/api-reference/port-api/) for details.


---

## 📚 Documentation & Resources

- [Port Docs – Self-Service Actions](https://docs.port.io/actions-and-automations/create-self-service-experiences/)
- [Port Docs – Backend Setup](https://docs.port.io/actions-and-automations/setup-backend/)
- [Port API Reference](https://docs.port.io/api-reference/port-api/)
- [Port Blog – Developer Self-Service](https://www.port.io/blog/developer-self-service-in-your-internal-developer-portal)
