# Continues Integration (CI) 

In order to understand the mechanism of CI, it's important to first define Git workflows.

**Git workflows** refer to the different approaches or strategies that teams can adopt when using Git in their software development projects.

[Gitflow](https://nvie.com/posts/a-successful-git-branching-model/) (by Vincent Driessen at nvie) is one of the most popular git branching models that involves the use of feature branches and multiple primary branches.
In this tutorial we will implement a **simplified** version of Gitflow.

## The `main` branch 

The `main` branch is the **primary branch** where the source code of `HEAD` always reflects the **production** state.

**When a developer wants to add a new feature or fix a bug, they create a _feature branch_ from an up-to-date `main` branch.**

1. In your project repo, run:
    
   ```bash
   git checkout main
   git pull origin main
   git checkout -b feature/my_new_feature
   ```
      
   This creates a new branch called `feature/my_new_feature` based on the latest `main` branch.

2. Make some small code change and commit it. 
3. Push your feature branch to remote:
    
   ```bash
   git push origin feature/my_new_feature
   ```
   
## Pull Requests

When the feature is complete and ready to be merged into `main`, the developer creates a **Pull Request** (PR) from their feature branch into `main`.

1. In your GitHub repo, click on the **Pull requests** tab.
2. Click the green **New pull request** button.
3. Select the base branch as `main` and the compare branch as `feature/my_new_feature`.
4. Click **Create pull request**.
5. Review the changes and click **Merge pull request** to merge the feature branch into `main`.

> [!NOTE]
> In real life some of your colleagues will be assigned to review your code before merging.


# Exercises 

### :pencil2: Protect branch `main`

It's very important to protect the `main` branch to avoid direct pushes that could introduce bugs or break the production code.

GitHub allows you to set up **branch protection rule** to enforce certain workflows and requirements before changes can be merged into a protected branch.

After setting the protection rule, make sure that no one can push directly to `main` branch (**even you** as repository admin).

### :pencil2: Test workflow in Pull Requests

Pull request is the main **gate** with which developers can merge their changes into `main` (into production).
Hence, performing an extensive set of automated tests in the PR is crucial to ensure that no bugs are introduced into production.

Let's implement a simple test workflow using GitHub Actions. 

1. In your project repo, create a new feature branch from an up-to-date `main` branch:
   ```bash
   git checkout main
   git pull origin main
   git checkout -b feature/ci_test_workflow
   ```
2. Create a new directory called `.github/workflows` if it doesn't already exist.
3. Inside the `workflows` directory, create a new file called `test.yaml`.

```yaml
name: CI checks

on: 
  pull_request:
    branches: 
     - main

permissions:
  contents: read

jobs:
  tests:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Unit Tests (placeholder)
        run: |
          echo "TODO: Implement unit tests here"
          # npm run test

      - name: API Tests (placeholder)
        run: |
          echo "TODO: Add API integration tests here"
          # curl http://localhost:8000/health
          # npm run test:api

      - name: Linting (placeholder)
        run: |
          echo "TODO: Add linting commands here"
          # npm run lint

      - name: Security Scan (placeholder)
        run: |
          echo "TODO: Add security scanning step here"
          # npm audit --audit-level=high
```

4. Commit and push the changes to your feature branch:
5. Create a Pull Request from `feature/ci_test_workflow` into `main`. Make sure the CI checks run and pass before merging.


Now let's integrate one of your existing tests into the above CI workflow to see it in action (since it's a new feature, you should create a new feature branch from an up-to-date `main` branch).


### :pencil2: Protect PR from being merged if CI checks fail

It's important to ensure that no code is merged into `main` without passing the CI checks.

Edit your **branch protection rule** so that it requires the CI checks to pass before allowing a merge into `main`.


