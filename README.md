# GitHub Actions for Azure Machine Learning Training

## Overview

I set up this workflow while learning about MLOps and CI/CD automation. The repo is forked from Microsoft's MLOps examples, and I've been exploring how to automatically trigger Azure ML training jobs through GitHub pull requests. It's been a great way to understand how model training can be integrated into a development workflow without manually kicking off jobs every time.

Basically, whenever I create a pull request, the workflow kicks in automatically and spins up an Azure ML training job. No manual steps needed—it all just happens in the background.

## Workflow: Manually Trigger an Azure Machine Learning Job

### Workflow File
- **Location**: `.github/workflows/` (check your repo for the exact filename)
- **Trigger**: Manual pull request workflow

## How It Works

When you push a PR, the workflow runs through these steps:

1. **Set up job** (1s) — Gets the runner ready
2. **Check out repo** (0s) — Grabs the latest code
3. **Install az ml extension** (26s) — Adds Azure ML CLI tools
4. **Azure login** (8s) — Authenticates with Azure using stored credentials
5. **Run Azure Machine Learning training job** (10s) — Submits the actual training job
6. **Post Azure login** (0s) — Cleanup
7. **Post Check out repo** (0s) — More cleanup
8. **Complete job** (1s) — Done

**Total time**: Around 45-50 seconds from PR to training job submitted.

Pretty cool to watch it all happen automatically instead of having to manually run commands.

## Triggering the Workflow

### Automatic (What I Usually Do)
Just create a pull request and it runs automatically. Push changes, open the PR, and watch the magic happen in the Actions tab.

### Manual (If You Need To)
If you want to manually trigger it for some reason:
1. Go to the **Actions** tab
2. Pick the **"Manually trigger an Azure Machine Learning job"** workflow
3. Hit **"Run workflow"**
4. Select your branch and click **"Run workflow"**

Pretty straightforward.

## Checking On Your Training Job

1. **Actions tab** — Click it to see all workflow runs
2. **Find your run** — Look for the PR or timestamp that matches what you just pushed
3. **Click into it** — See each step and the logs
4. **Green checkmark** = success, **Red X** = something broke

You'll usually see the job submitted successfully in the logs, then you can jump over to Azure ML Studio to watch the actual training happen.

### Sample Workflow Log
```
✓ Set up job
✓ Check out repo
✓ Install az ml extension
✓ Azure login
✓ Run Azure Machine Learning training job
✓ Post Azure login
✓ Post Check out repo
✓ Complete job
```

## What You Need to Get This Running

### On Azure's Side
- Azure subscription with an active ML workspace
- Some compute resources set up in Azure ML for training
- A service principal for authentication (basically credentials that let GitHub talk to Azure)

### On GitHub
You'll need to add some secrets to your repo settings. These let the workflow authenticate with Azure:
- `AZURE_SUBSCRIPTION_ID` — Your subscription ID
- `AZURE_CREDENTIALS` — Service principal credentials (as JSON)
- `AZURE_ML_WORKSPACE` — Your workspace name
- `AZURE_ML_RESOURCE_GROUP` — Your resource group name

### Locally (If You're Developing)
- Azure CLI installed
- The Azure ML extension for the CLI
- Python 3.8+

## Setting Things Up

### Adding Secrets to GitHub

1. Go to your repo **Settings** → **Secrets and variables** → **Actions**
2. Click **"New repository secret"**
3. Add each one:
   - `AZURE_SUBSCRIPTION_ID`
   - `AZURE_CREDENTIALS`
   - `AZURE_ML_WORKSPACE`
   - `AZURE_ML_RESOURCE_GROUP`

That's the annoying part, but once it's done you're golden.

### The Workflow File

The actual workflow (check `.github/workflows/`) is pretty simple. It basically:
1. Installs the Azure ML CLI
2. Logs into Azure using your secrets
3. Submits a training job using a YAML config file
4. Done

## A Real Example

Here's basically what happens when I make changes:

```bash
# 1. Make changes to the code (model, training script, whatever)
git checkout -b feature/model-improvements

# 2. Commit and push
git add .
git commit -m "Improve model architecture"
git push origin feature/model-improvements

# 3. Open a PR on GitHub
# Instantly, the workflow kicks off automatically
```

Then I just sit back and watch:
- **GitHub Actions** shows me the workflow steps
- Once it completes, **Azure ML** shows me the training job running
- I can check metrics, see how the model's doing, all that good stuff

No manual job submission, no running commands from my terminal. It's all automated.

## Troubleshooting

### When Things Break

| What Went Wrong | How I Usually Fix It |
|---|---|
| Azure login failed | Check the `AZURE_CREDENTIALS` secret—make sure it's valid JSON |
| Can't find az ml command | Might need to update Azure CLI; the extension wasn't installed |
| Job won't submit | Double-check the job config file path and that you have permissions in the workspace |
| Everything times out | Might not have enough compute resources available, or the job config is way too big |

### Finding Errors

1. Click the failed run in the **Actions** tab
2. Expand the step that failed (usually the "Run Azure Machine Learning training job" step)
3. Read the error message—it usually tells you what's wrong
4. Google it or check Azure ML docs if it's confusing

## Lessons I've Learned

1. **Test locally first** — Before relying on the workflow, make sure your job config actually works
2. **Watch your costs** — Azure ML compute isn't free. I monitor job runs to avoid surprises
3. **Give jobs meaningful names** — Makes it way easier to find them later in the job history
4. **Don't forget to clean up** — Old job runs pile up. Delete them to keep costs down and the workspace clean
5. **Keep scripts in version control** — Makes it easy to track what changed between runs
6. **Set reasonable timeouts** — Otherwise a broken job might run for hours and burn money

## Resources

- [Azure ML CLI Docs](https://learn.microsoft.com/en-us/azure/machine-learning/reference-azure-machine-learning-cli)
- [GitHub Actions Docs](https://docs.github.com/en/actions)
- [Azure ML Training Jobs](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-train-model)

**Original repo**: This is forked from [Microsoft's MLOps GitHub repository](https://github.com/microsoft/MLOps-Demo) — great resource for learning how to structure ML projects.

---

**Status**: Working and learning!  
**Last update**: March 2026
