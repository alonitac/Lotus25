# Final Task: Containerize and Deploy Your Microservices

## Containerize the services

In this task, you will containerize your frontend and backend microservices, and create a CI/CD pipeline that builds and deploys the application to an EC2 instance using GitHub Actions.

1. Create a `Dockerfile` in your backend and frontend service directories.
2. Don't forget to create a `.dockerignore` file to exclude unnecessary files (e.g., `node_modules`, `__pycache__`, `.git`, `.env`)
3. Build and test the images locally. For example:

```bash
cd backend
docker build -t backend:0.0.1 .
docker run -p 8080:8080 backend:0.0.1
```

```bash
cd frontend
docker build -t frontend:0.0.1 .
docker run -p 3000:3000 frontend:0.0.1
```

> [!NOTE]
> - **Bonus**: try to make the image sizes as small as possible. 
> - **Bonus**: try to make the time it takes to build the images as short as possible.

4. Create a `docker-compose.yaml` file to orchestrate both services. Upon:

```bash
docker compose up
```

Both services should start and be able to communicate with each other.

[!TIP]
If you want to include sensitice valus in your `dockker-compose.yaml` and don't want to hardcode them (you shouldn't do that) - use environment variables or `.env` file.

Compose lets you define environment variables in a `.env` file so you don’t hardcode values in your `docker-compose.yaml`.

Simply create a `.env` file **in the same directory** as your `docker-compose.yaml`, as follows:

```text
# .env file
MY_PASS=1234
```

Then, reference the variable in your `docker-compose.yaml` like this:

```yaml
services:
    backend:
        image: backend:0.0.1
        ports:
          - "8080:8080"
        environment:
          - NODE_ENV=production
          - PORT=8080
          - DB_PASSWORD=${MY_PASS}
```


## CI/CD Pipeline with GitHub Actions

In `.github/workflows/`, create two YAML workflow files: one for CI (testing) and one for CD (deployment).

You can use the following skeletons:

```yaml
# .github/workflows/ci.yaml

name: CI - Test and Build

on: 
  pull_request:
    branches: 
      - main

jobs:
  frontend-test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run frontend tests
        run: |
          cd frontend
          echo "Running frontend tests..."
          # e.g., npm install && npm test

  backend-test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run backend tests
        run: |
          cd backend
          echo "Running backend tests..."
          # e.g., npm install && npm test      
```

And for the deploy workflow:


```yaml
name: CD - Deploy to EC2

on:
  push:
    branches: 
      - main

jobs:
  build-frontend:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Build & push
        run: |
          
          # don't forget to store the DOCKER_USERNAME and DOCKER_PASSWORD secrets in your repo settings
          
          docker login -u ${{ secrets.DOCKER_USERNAME }} -p ${{ secrets.DOCKER_PASSWORD }}
          
          cd frontend
          docker build -t ${{ secrets.DOCKER_USERNAME }}/frontend:latest .
          docker push ${{ secrets.DOCKER_USERNAME }}/frontend:latest

  build-backend:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Build & push
        run: |
          docker login -u ${{ secrets.DOCKER_USERNAME }} -p ${{ secrets.DOCKER_PASSWORD }}
          
          cd backend
          docker build -t ${{ secrets.DOCKER_USERNAME }}/backend:latest .
          docker push ${{ secrets.DOCKER_USERNAME }}/backend:latest
      
  deploy:
    needs: build-and-push
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Deploy to server
        uses: appleboy/ssh-action@v1.0.3
        with:
          # Make sure to store the EC2_SSH_PRIVATE_KEY and EC2_HOST secrets in your repo settings
          host: ${{ secrets.EC2_HOST }}
          username: ubuntu
          key: ${{ secrets.EC2_SSH_PRIVATE_KEY }}
          script: |
            cd /home/ubuntu/YOUR_REPO_NAME || (git clone https://github.com/YOUR_USERNAME/YOUR_YOLO_REPO_NAME.git && cd YOUR_YOLO_REPO_NAME)
            
            docker compose pull
            docker compose up -d
```

> [!TIP] #### Setup GitHub Secrets
> 
> In your GitHub repository, go to **Settings → Secrets and variables → Actions** and add:
> 
> - `DOCKER_USERNAME` - Your Docker Hub username
> - `DOCKER_PASSWORD` - Your Docker Hub password/token
> - `EC2_SSH_PRIVATE_KEY` - Your EC2 instance SSH private key
> - `EC2_HOST` - Your EC2 instance public IP or hostname

## Test the full workflow

1. Checkout and pull branch `main`.
2. Create a new feature branch, commit & push a small change
3. Create a Pull Request
4. Verify CI checks run and pass
5. Merge the PR into `main`
6. Verify CD pipeline deploys to EC2
7. Access your application on EC2 public IP, make sure the view reflects your changes


