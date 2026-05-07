<div align="center">

# PA4 Submission: TaskFlow Pipeline

<img alt="GitHub only" src="https://img.shields.io/badge/Submit-GitHub%20URL%20Only-10b981?style=for-the-badge">
<img alt="Total points" src="https://img.shields.io/badge/Total-100%20points-7c3aed?style=for-the-badge">

</div>

<div style="background:#f5f3ff;color:#111827;border-left:6px solid #6330bc;padding:14px 18px;border-radius:10px;margin:18px 0;">
Copy this file to <code style="color:#111827;background:#ddd6fe;padding:2px 4px;border-radius:4px;">SUBMISSION.md</code>. Put every screenshot in <code style="color:#111827;background:#ddd6fe;padding:2px 4px;border-radius:4px;">docs/</code>, embed it under the correct task, and write a short description below each image explaining what it proves. The grader should not need any file outside this repository.
</div>

## Student Information

| Field | Value |
|---|---|
| Name | Maham Waseem |
| Roll Number | 26100173 |
| GitHub Repository URL | https://github.com/Maham-Waseem03/CS487-PA4 |
| Resource Group | `rg-sp26-26100173` |
| Assigned Region | `swedencentral` |

## Evidence Rules

- Use relative image paths, for example: `![AKS nodes](docs/aks-nodes.png)`.
- Every image must have a 1-3 sentence description below it.
- Azure Portal screenshots must show the resource name and enough page context to identify the service.
- CLI screenshots must show the command and output.
- Mask secrets such as function keys, ACR passwords, and storage connection strings.


## Task 1: App Service Web App (15 points)

### Evidence 1.1: Forked Repository

![Forked Repo](docs/forked_repo.png)

Description: This is my working fork that contains the PA4 starter structure and connects to my deployment pipeline.

### Evidence 1.2: App Service Overview

![Webapp_task1](docs/webappoverview.png)

Description: The webapp was created in swedencentral due to quota issues. The url is https://pa4-26100173.azurewebsites.net/. The resource group is rg-sp26-26100173, and the screenshot shows the running status of the webapp.

### Evidence 1.3: Deployment Center / GitHub Actions

![Deployment Center](docs/depploymentcenter.png)

Description: The webapp was connected to the GitHub repository containing the fork, and GitHub Actions successfully deployed the source code to the App Service.  

### Evidence 1.4: Live Web UI

![Task flow page](docs/dashboard_task1.png)

Description: The App Service is serving the frontend React application successfully over the public internet.

---

## Task 2: Azure Container Registry (15 points)

### Evidence 2.1: ACR Overview

![ACR Overview](docs/acr-overview.png)

Description: The Container Registry (`pa426100173`) was deployed using the Basic SKU within the `rg-sp26-26100173` resource group.

### Evidence 2.2: Docker Builds

![builds successful](docs/images%20pushed.png)

Description: The `validate-api:v1` image was built from the validate-api directory, `report-job:v1` from the report-job directory, and `func-app:v1` from the function-app directory.

### Evidence 2.3: ACR Repositories

![ACR repos](docs/acr%20repo%20list.png)

Description: This confirms that `validate-api:v1`, `report-job:v1`, and `func-app:v1` were successfully tagged and pushed to the remote Azure registry.

---

## Task 3: Durable Function Implementation (12 points)

### Evidence 3.1: Completed Function Code

![function_app.py](function-app/function_app.py)

Description: The orchestrator checks the validation activity, and if the validation passes (`valid: true`), it successfully calls the report activity to generate the PDF before completing the workflow.

### Evidence 3.2: Local Function Handler Listing

![func start](docs/func%20start.png)

Description: The local Core Tools log shows that the Durable Functions runtime successfully discovered the HTTP starter, the orchestrator, and both activity handlers.

### Evidence 3.3: Local Validator Test
![curl command](docs/local%20smoke%20test%20curl%20command.png)
![test result](docs/local%20validator%20test.png)

Description: The orchestrator was triggered using the local curl command, and the output verifies that the mocked validation step passed successfully.

---

## Task 4: Function App Container Deployment (8 points)

### Evidence 4.1: Function App Container Configuration

![function app config](docs/image_config_in%20func_app.png)
![functions list](docs/functions_list.png)

Description: The Function App is configured to pull the `pa426100173.azurecr.io/func-app:v1` container image, and the portal successfully lists the unpacked functions.

### Evidence 4.2: Orchestration Smoke Test

![curl output](docs/task4_curl.png)

Description: The returned `id` proves the orchestrator successfully started a new instance, and the `statusQueryGetUri` provides the endpoint for the frontend to asynchronously poll the progress.

### Evidence 4.3: Expected Failed Status Before Downstream Wiring

![curl results](docs/failed_task4.png)

Description: This failure is expected because the orchestrator attempts to reach the AKS validator via the `VALIDATE_URL` setting, which has not been deployed or wired up yet.

---

## Task 5: AKS Validator (15 points)

### Evidence 5.1: AKS Cluster

![AKS overview](docs/aks%20overview.png)

Description: The AKS cluster is running 1 node of size Standard_B2s located within the `rg-sp26-26100173` resource group.

### Evidence 5.2: Kubernetes Nodes and Pods

![get nodes results](docs/get%20nodes.png)
![get pods results](docs/get%20pods.png)

Description: The node is in a Ready state, and the validator pod has been successfully scheduled and is Running.

### Evidence 5.3: Kubernetes Service

![External IP](docs/ip,health,validate%20tests.png)

Description: The `validate-service` provisions an Azure LoadBalancer that exposes an `EXTERNAL-IP` (e.g., 20.68.x.x) on port 8080 to the public internet.

### Evidence 5.4: Validator API Tests

![Health and Validate tests](docs/ip,health,validate%20tests.png)

Description: A valid order correctly returns `{"valid": true}`. The API enforces a strict rejection rule returning `{"valid": false}` for any order payload where the quantity exceeds 100.

### Evidence 5.5: Function App `VALIDATE_URL`

![VALIDATE_URL in App settings](docs/validate_url%20env.png)

Description: The Durable Function uses the `VALIDATE_URL` application setting at runtime to route HTTP POST requests directly to the AKS LoadBalancer's external IP.

### Evidence 5.6: AKS Idle Behavior

![AKS Metrics](docs/aks%20metrics.png)

Description: The metrics show that the AKS nodes and pods maintain a persistent baseline of CPU/Memory usage and remain active (incurring costs) even when zero validation requests are being handled.

---

## Task 6: ACI Report Job (15 points)

### Evidence 6.1: Blob Container

![proof of report blob](docs/reports_blob_created.png)

Description: The generated PDF reports are permanently stored in the `reports` container within the securely managed Azure Blob Storage account.

### Evidence 6.2: Manual ACI Run

![az container show result](docs/ci-report-test.png)

Description: The container state reaches `Succeeded` (Terminated with exit code 0). It exits because it is a one-shot batch job designed to execute the Python script once and immediately shut down.

### Evidence 6.3: ACI Logs

![az container logs](docs/container-logs-blobs-list.png)

Description: The logs confirm the initialization of the container, the execution of the PDF generation script, and the successful upload stream to Blob Storage.

### Evidence 6.4: Generated PDF

![blob list command](docs/container-logs-blobs-list.png)
![portal showing blobs](docs/TEST001.png)

Description: The presence of `TEST-001.pdf` in the blob list (and the associated XML access denial log) provides strict proof that the ACI successfully wrote the file to the restricted storage account.

### Evidence 6.5: Function App Managed Identity and IAM

![managed identity](docs/func%20app%20mi.png)

Description: The Function App utilizes a User-Assigned Managed Identity so it possesses the necessary Azure Resource Manager (ARM) permissions to programmatically provision and delete ACI resources.

### Evidence 6.6: Report App Settings

![env variables in app settings](docs/validate_url%20env.png)

Description: `REPORT_*` defines the image and resource group for the ACI creation. `ACR_*` supplies the credentials to pull the image, and `STORAGE_ACCOUNT_URL` routes the generated PDF to the correct blob container.

---

## Task 7: End-to-End Pipeline (15 points)

### Evidence 7.1: Web App Wiring

![web app settings](docs/webappsettings.png)

Description: The frontend utilizes the `FUNCTION_START_URL` to POST new orders to the orchestrator, and polls the `FUNCTION_STATUS_URL` to track the background job's progress.

### Evidence 7.2: Happy Path UI

![before submitting](docs/happyorderbeforesubmit.png)
![running](docs/happy%20order%20running.png)
![pdf generated](docs/happyorderaccepted.png)

Description: A valid payload (Qty < 100) is submitted. The UI transitions to a running state while polling, ultimately resolving with a clickable URL to the final PDF report.

### Evidence 7.3: Backend Participation

![backend logs 1](docs/happybackend1.png)
![backend logs 2](docs/happybackend2.png)
![backend logs 3](docs/happybackend3.png)

Description: The logs trace the entire lifecycle: the orchestrator wakes up, the AKS validator processes the payload and returns `200 OK`, and the ACI is dynamically spun up to generate the PDF.

### Evidence 7.4: Reject Path UI

![rejected order](docs/badorder.png)
![logs showing no ACI created](docs/badorderlogs.png)

Description: The AKS validator rejected the order due to an invalid quantity. The orchestrator detected this failure and short-circuited, correctly preventing the creation of an ACI instance to save compute resources.

---

## Task 8: Write-up and Architecture Diagram (5 points)

### Evidence 8.1: Architecture Diagram

![Architecture Diagram](docs/Architecture%20diagram.png)

Description: The diagram illustrates the complete data flow from the GitHub-deployed App Service, through the Durable Function orchestrator, reaching out to the AKS validator and dynamically provisioning the ACI for blob storage writes.

### Question 8.2: Service Selection

* **App Service:** Chosen for the frontend because it provides a fully managed, scalable environment for hosting web applications with built-in integration for GitHub Actions (CI/CD).
* **Durable Functions:** Acts as the central stateful orchestrator. It manages the asynchronous sequence of calling the validator and report generator, keeping track of state so progress isn't lost during long-running tasks.
* **AKS (Kubernetes):** Selected for the Validator microservice. Since validation is a fast, frequently called HTTP endpoint, AKS provides the persistent, high-availability infrastructure required for standard enterprise APIs.
* **ACI (Container Instances):** Ideal for the Report Job because it is a "Serverless Container." It allows the architecture to spin up a compute resource for 30 seconds to generate a PDF and then delete it, ensuring billing only occurs for the exact seconds used.

### Question 8.3: ACI vs AKS

AKS is designed for long-running, persistent services; its nodes remain "warm" and continue to incur hourly costs even when no orders are being processed. ACI operates on an ephemeral, serverless container model; it costs $0 when idle and only bills for the specific CPU and memory used during the brief lifespan of the report generation. Operationally, AKS requires complex cluster and load balancer management, whereas ACI simply runs a single Docker image on demand.

### Question 8.4: Durable Functions vs Plain HTTP

1.  **State Management:** If a server crashes mid-way through a long PDF generation, a plain HTTP request would fail and lose all data. Durable Functions checkpoints progress automatically, allowing it to resume exactly where it left off.
2.  **Asynchronous Polling (Timeouts):** Standard HTTP connections will time out if a backend process takes several minutes. Durable Functions solves this by instantly returning a `statusQueryGetUri`, allowing the frontend client to safely poll for updates asynchronously.

### Question 8.5: Cost Review

![Cost Management](docs/cost-analysis.png)

Description: The AKS cluster (specifically the Virtual Machine underlying the `Standard_B2s` node) is the most expensive resource. Unlike the consumption-based Function App and ACI which only charge per execution, the AKS node runs continuously 24/7.

### Question 8.6: Challenges Faced

1.  **Strict Security Policies (RBAC/Keys):** I encountered environment-level policy blocks (`KeyBasedAuthenticationNotPermitted` and `AuthorizationFailed`) that prevented me from using connection strings or manually assigning the `Storage Blob Data Contributor` role to the Managed Identity. I adapted to this by utilizing SAS tokens/configuration workarounds and relying heavily on the Kudu console (Advanced Tools) to extract vital runtime logs when standard monitoring was restricted.
2.  **Cross-Region Defaults and CLI Parsing:** My Resource Group was deployed in `ukwest` while resources like the Function App were in `swedencentral`, causing the Azure CLI to default my AKS cluster to the UK, which introduced cross-region networking variables. Additionally, PowerShell's strict string parsing mangled my JSON payloads during the AKS `curl` smoke tests, requiring me to isolate the commands in a standard `cmd` environment to successfully transmit the HTTP requests.

---
