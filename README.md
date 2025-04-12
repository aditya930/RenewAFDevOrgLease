# RenewAFDevOrgLease

Much improved readme contents contributed by [Warren Walters](https://github.com/walters954)

Automatically renew the "lease" on Agentforce enabled Developer Edition orgs using GitHub Actions.

See the [blog post](https://bobbuzzard.blogspot.com/2025/03/keep-your-agentforce-dev-org-alive-and.html) 

## Why This Repo Exists

Salesforce Developer Editions with Agentforce and Data Cloud expire after just 45 days of inactivity. While manually logging in every 6 weeks might seem manageable, it's easy to forget. This GitHub Action automates the process by running a weekly job that logs into your dev org(s) and performs a simple query to keep them active.

## Setup Instructions

### 1. Setup Your Dev Org(s)

1. Sign up for an Agentforce dev org at: https://www.salesforce.com/form/developer-signup/?d=pb
2. Connect it to the Salesforce CLI:
    ```
    sf org login web -o YourOrgAlias
    ```
3. Get the authentication URL:
    ```
    sf org display -o YourOrgAlias --json --verbose
    ```
4. Copy the **Sfdx Auth Url** value from the output.

### 2. Configure GitHub Repository

1. Fork or clone this repository (note: for free GitHub accounts, the repository needs to be public)
2. Navigate to Settings → Environments → New environment
3. Create a new environment
4. Add an environment secret for each org you want to renew with the name matching the matrix entries in the workflow file
    - Secret name should match entries in the matrix (e.g., `trailhead_org1` or `devedition_org1`)
    - Secret value should be the **Sfdx Auth Url** from step 1.4

### 3. Customize the Workflow (Optional)

The `.github/workflows/renew.yaml` file uses a matrix strategy to handle multiple orgs:

```yaml
authenticate-orgs:
    runs-on: ubuntu-latest
    strategy:
        matrix:
            org: [AGENTFORCE_DEV_ORG_URL, DEVEDITION_ORG1]
```

You can customize this matrix by:

1. Modifying the org names in the array to match your environment secret names
2. Adding or removing entries based on how many orgs you need to maintain

### 4. Run the Workflow

The workflow can be triggered in two ways:

-   Automatically via the cron schedule (Monday at 20:30 UTC)
-   Manually by going to the Actions tab and using the "Run workflow" button

## How It Works

1. The action runs on a schedule (currently Monday evenings)
2. It sets up Node.js and installs the Salesforce CLI
3. For each org defined in the matrix:
    - It authenticates using the stored SFDX Auth URL
    - It queries the org which registers as login activity
    - This resets the 45-day inactivity timer

By running weekly, this provides plenty of buffer in case of temporary failures.
