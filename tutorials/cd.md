# Continues Deployment (CD)

Once new code changes pass the CI pipeline, they are automatically deployed to a dev or production environment through the CD pipeline.

## Creating a CD Pipeline

Here is an example of a GitHub Actions workflow that deploys code to Railway upon pushing to the main branch:

```yaml
name: Deploy to Railway

on:
  push:
    branches: 
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Install Railway CLI
      run: npm install -g @railway/cli
    
    - name: Deploy to Railway
      env:
        RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}
        
      run: railway up --service ${{ secrets.RAILWAY_SERVICE_ID }}
```

#### Notes

- The above workflow triggers on every push to the `main` branch. But since the only way to push to `main` is through a PR that has passed CI checks, you can be confident that only tested code gets deployed.
- Make sure to set the [`RAILWAY_TOKEN` and `RAILWAY_SERVICE_ID` secrets](https://blog.railway.com/p/github-actions) in your GitHub repository settings for authentication.
- You should deploy to a [production environment](https://docs.railway.com/guides/environments) in Railway. 


# Exercises 

### :pencil2: Implement a CD Pipeline

Implement the above CD pipeline in your project repository. 

Consult the [Railway CLI documentation](https://docs.railway.app/develop/cli) for more details on how to use the Railway CLI for deployments.

Test it by merging a PR into the `main` branch and verify that the changes are deployed to your Railway service.

### :pencil2: Rollback Mechanisms

In CI/CD pipelines, it's essential to have **rollback mechanisms** in place to quickly revert to a stable state in case of deployment failures or critical bugs.

In GitHub Actions, you can implement rollback to any previously successful deployment simple by redeploying that workflow run.

1. Go to the "Actions" tab in your GitHub repository.
2. Find the last successful deployment workflow run.
3. Click on the run and then click the "Re-run jobs" button to redeploy that version.

Make sure to test this rollback process to ensure you can quickly revert to a stable state when needed.

