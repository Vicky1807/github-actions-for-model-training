# GitHub Actions for Azure Machine Learning Training

## Overview

This repository includes an automated GitHub Actions workflow that triggers Azure Machine Learning (Azure ML) training jobs directly from pull requests. This enables seamless integration of model training into your CI/CD pipeline.

## Workflow: Manually Trigger an Azure Machine Learning Job

### Workflow File
- **Location**: `.github/workflows/manual-tigger-job.yml`
- **Trigger**: Manual pull request workflow

### How It Works

The workflow executes the following steps in sequence:

1. **Set up job** (1s)
   - Initializes the GitHub Actions runner environment

2. **Check out repo** (0s)
   - Clones your repository code into the runner

3. **Install az ml extension** (26s)
   - Installs the Azure ML CLI extension for command-line operations

4. **Azure login** (8s)
   - Authenticates with your Azure subscription using configured credentials

5. **Run Azure Machine Learning training job** (10s)
   - Executes the training job using Azure ML's job submission

6. **Post Azure login** (0s)
   - Cleans up Azure authentication tokens

7. **Post Check out repo** (0s)
   - Final cleanup step

8. **Complete job** (1s)
   - Workflow execution completes

**Total Execution Time**: ~46-48 seconds

### Triggering the Workflow

1. Navigate to the **Actions** tab in your GitHub repository
2. Select the **"Manually trigger an Azure Machine Learning job"** workflow
3. Click **"Run workflow"**
4. Choose your target branch (usually `main`)
5. Click **"Run workflow"** to execute

### Viewing Results

1. **Go to Actions Tab**: Click the **Actions** tab in your GitHub repository
2. **Select the Workflow Run**: Find the run you want to inspect
3. **Review the Logs**: Click on each step to view detailed execution logs
4. **Check Status**: Look for the green checkmark (✓) indicating success or red (✗) for failures

## Prerequisites

Before using this workflow, ensure you have:

### Azure Setup
- An Azure subscription with an active Azure ML workspace
- Azure ML compute resources configured for training
- Service Principal credentials for authentication

### GitHub Setup
- GitHub repository secrets configured:
  - `AZURE_SUBSCRIPTION_ID`: Your Azure subscription ID
  - `AZURE_CREDENTIALS`: Your Azure service principal credentials (JSON format)
  - `AZURE_ML_WORKSPACE`: Your Azure ML workspace name
  - `AZURE_ML_RESOURCE_GROUP`: Your Azure resource group name

### Local Development
- Azure CLI installed (`az cli`)
- Azure ML CLI extension installed (`az ml`)
- Python 3.8+

## Configuration

### Setting Up Secrets

1. Go to your GitHub repository **Settings** → **Secrets and variables** → **Actions**
2. Click **"New repository secret"**
3. Add each required secret:
   - `AZURE_SUBSCRIPTION_ID`
   - `AZURE_CREDENTIALS`
   - `AZURE_ML_WORKSPACE`
   - `AZURE_ML_RESOURCE_GROUP`

### Workflow File Structure

The workflow file (typically named `azure-ml-training.yml`) contains:

```yaml
name: Manually trigger an Azure Machine Learning job
on:
  pull_request:
    types: [opened, synchronize]
  workflow_dispatch:

jobs:
  train:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install az ml extension
        run: |
          az extension add -n ml -y
      - name: Azure login
        uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
      - name: Run Azure Machine Learning training job
        run: |
          az ml job create --file <your-job-config>.yml
```

### Monitoring Job Progress

1. **In GitHub Actions**:
   - View real-time logs for each step
   - Check if the training job submitted successfully

2. **In Azure ML Studio**:
   - Navigate to your Azure ML workspace
   - Go to **Jobs** section
   - Find the job triggered by the workflow
   - Monitor training metrics and outputs

## Troubleshooting

### Issues Faced

| Issue | Solution |
|-------|----------|
| **Authentication failed** | Verify `AZURE_CREDENTIALS` secret is correctly formatted JSON |
| **az ml extension not found** | Check Azure CLI version compatibility; may need to update |
| **Job creation failed** | Verify job config file path and Azure ML workspace permissions |
| **Timeout during training** | Increase timeout limits in workflow or check compute resource availability |

---

**Last Updated**: March 2026  
**Status**: Successfully tested and operational
