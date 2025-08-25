# Week 07: Continuous Integration (CI) Pipeline for Backend Microservices

## Continuous Integration Explained

Continuous Integration (CI) is a development practice where developers frequently merge their code changes into a central repository. Each merge triggers an automated build and test process. The primary goals of CI are to:

- **Detect integration issues early:** Catch conflicts and bugs quickly, reducing the effort required to fix them.
- **Improve code quality:** Enforce coding standards and run automated tests on every change.
- **Produce releasable artifacts:** Generate deployable software versions (e.g., Docker images) after successful validation.

### 1. Fork the Repository

To begin, fork this repository to your own GitHub account. This will create a copy of the project under your personal namespace, allowing you to make changes and set up GitHub Actions without affecting the original repository.

1.  Go to the original repository's page on GitHub.
2.  Click the "Fork" button in the top right corner.

Once forked, clone _your forked repository_ to your local machine:

```bash
git clone [https://github.com/your-username/your-forked-repository.git](https://github.com/your-username/your-forked-repository.git) # Replace with your actual forked repo URL
cd your-forked-repository/week07/example-01 # Adjust path as needed for Week 07 Example 01
```

### 2. Create GitHub Repository Secrets

Your GitHub Actions workflow will need credentials to interact with Azure and ACR. Create these as secrets in your GitHub repository:

1.  Go to your GitHub repository.
2.  Navigate to **Settings** > **Secrets and variables** > **Actions**.
3.  Click **New repository secret**.

- **`AZURE_CREDENTIALS`**: This JSON object allows GitHub Actions to authenticate with Azure.
  To create the necessary Azure Service Principal, follow the official Microsoft Learn documentation:
  [How to create a service principal for an Azure application using the portal](https://learn.microsoft.com/en-us/entra/identity-platform/howto-create-service-principal-portal#register-an-application-with-microsoft-entra-id-and-create-a-service-principal)

  Once you have created the Service Principal, you will find the required values for `clientId`, `clientSecret`, `subscriptionId`, and `tenantId`. Use these to construct the JSON secret:

  ```json
  {
    "clientId": "<Client ID>",
    "clientSecret": "<Client Secret>",
    "subscriptionId": "<Subscription ID>",
    "tenantId": "<Tenant ID>"
  }
  ```

  Paste this complete JSON object as the value for the `AZURE_CREDENTIALS` secret.

  - **`ACR_LOGIN_SERVER`**: The full login server name of your Azure Container Registry (e.g., `myacr.azurecr.io`).

    You can find this by navigating to your Azure Container Registry in the Azure portal, selecting "Access keys" under "Settings", and locating the "Login server" value. Add this value as the `ACR_LOGIN_SERVER` secret.

## 3. Triggering the CI Pipeline

The CI pipeline is configured to trigger automatically in the following scenarios:

- On `push` to the `main` branch: Any code changes merged into the `main` branch will start the CI workflow.

- On changes to `backend/\*\*` paths: The pipeline is optimized to run only when relevant backend code or the workflow file itself changes.

- Manually via `workflow_dispatch`: You can trigger the workflow manually from the GitHub Actions tab in your repository. Go to the "Actions" tab, select "Backend CI - Test, Build and Push Images to ACR" from the workflows list, and click "Run workflow".

## 4. Verifying CI Pipeline Execution

After triggering the pipeline, you can monitor its progress and results:

1. Navigate to the Actions tab: In your GitHub repository, click on the "Actions" tab.

2. Select the "Backend CI - Test, Build and Push Images to ACR" workflow: You will see a list of workflow runs. Click on the latest run.

3. Review job progress: You can see the `test_and_lint_backends` and `build_and_push_images` jobs executing.

4. Inspect logs: Click on any step within a job to view its detailed logs, including test results, linting output, and Docker build/push messages.

5. Successful completion: A green checkmark next to the workflow run indicates all jobs passed successfully, meaning your code is tested, linted, and images are pushed to ACR.
