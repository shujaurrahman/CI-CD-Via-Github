

# CI/CD Pipeline for Deploying to InfinityFree with GitHub Actions

This repository demonstrates setting up a Continuous Integration and Continuous Deployment (CI/CD) pipeline using GitHub Actions to deploy code to an InfinityFree server via FTP. With CI/CD, every change pushed to the main branch is automatically deployed to your server, making it faster and more efficient to keep your website up-to-date.

## Table of Contents
1. Introduction to CI/CD and GitHub Actions
2. How the Workflow Works
3. Setting Up InfinityFree for Deployment
4. Creating the GitHub Actions Workflow
5. Configuring GitHub Secrets for Secure FTP
6. Triggering the Workflow
7. Verifying the Deployment
8. Troubleshooting Common Issues



## 1. Introduction to CI/CD and GitHub Actions

**Continuous Integration (CI)** and **Continuous Deployment (CD)** streamline the process of integrating code changes, running tests, and deploying code to production automatically. GitHub Actions is a powerful automation tool that allows you to define workflows to perform various actions, such as deploying code to a server.

With this setup:
- **CI** ensures that every code change is tested.
- **CD** automatically deploys the code to your server, allowing for faster, hands-free updates to your site on InfinityFree.



## 2. How the Workflow Works

The workflow in this repository is designed to:
1. **Check out the latest code** from the repository.
2. **Optionally run PHP linting** to ensure code quality.
3. **Deploy code via FTP** to InfinityFree, automating the process of uploading files to your web server whenever changes are made.

This workflow is triggered automatically whenever code is pushed to the `main` branch.



## 3. Setting Up InfinityFree for Deployment

1. **Sign in to InfinityFree**: Go to your [InfinityFree account](https://infinityfree.net) and log in.
2. **Locate FTP Credentials**: In your control panel, find the FTP details:
   - **FTP Server**: Usually something like `ftpupload.net`.
   - **FTP Username**: Your InfinityFree account username.
   - **FTP Password**: The password associated with your InfinityFree account.
3. **Identify Deployment Directory**: Note the directory where you want your website files to be deployed. For example, for a primary domain, the path might look like `/yourdomain.com/htdocs/`.

> **Important**: Make sure your target directory is correctly set up, as incorrect paths may cause deployment errors or overwrite files in the wrong location.


## 4. Creating the GitHub Actions Workflow

GitHub Actions workflows are defined in YAML files within the `.github/workflows/` directory of your repository.

### Steps to Create the Workflow File

1. **Create the Workflow Directory**:
   - In the root of your repository, create a folder named `.github/workflows`.

2. **Create the Workflow File**:
   - Inside `.github/workflows/`, create a file named `deploy.yml`. This file will contain all the steps for the deployment process.

3. **Add the Workflow Configuration**:
   - Copy and paste the following configuration into `deploy.yml`. This configuration defines the workflow for deploying your code to InfinityFree.

```yaml
name: Deploy to InfinityFree

on:
  push:
    branches:
      - main  # Adjust if you want to use a different branch for deployment

jobs:
  deploy:
    runs-on: ubuntu-latest  # This specifies the type of runner (virtual machine) to use

    steps:
    - name: Checkout code
      uses: actions/checkout@v2  # Checks out your code to the runner

    - name: Install PHP (optional for linting)
      uses: shivammathur/setup-php@v2  # Sets up a PHP environment
      with:
        php-version: '7.4'  # Version can be adjusted to match your server

    - name: Validate PHP Syntax (optional)
      run: |
        find . -name "*.php" -print0 | xargs -0 -n1 php -l  # Validates PHP syntax

    - name: Deploy to InfinityFree via FTP
      uses: SamKirkland/FTP-Deploy-Action@4.1.0
      with:
        server: ftpupload.net  # Replace with your actual FTP server
        username: ${{ secrets.FTP_USERNAME }}  # FTP username stored securely
        password: ${{ secrets.FTP_PASSWORD }}  # FTP password stored securely
        local-dir: ./  # Root directory of your repository
        server-dir: /yourdomain.com/htdocs/  # Target directory on InfinityFree
```

### Workflow Configuration Details

- **name**: Specifies the workflow’s name.
- **on**: Defines when the workflow should be triggered. Here, it’s set to run whenever changes are pushed to the `main` branch.
- **jobs**: Defines the jobs or tasks in the workflow.
  - **runs-on**: Specifies the runner environment (Ubuntu).
  - **steps**: Each individual action within the job:
    - **Checkout code**: Pulls the repository code onto the runner.
    - **Install PHP**: Installs PHP, which can be used to lint or test the code.
    - **Validate PHP Syntax**: An optional step to lint PHP files.
    - **Deploy to InfinityFree via FTP**: Deploys files using `SamKirkland/FTP-Deploy-Action`.


## 5. Configuring GitHub Secrets for Secure FTP

To protect your FTP credentials, store them as secrets in GitHub.

1. Go to **Settings** > **Secrets** > **Actions** in your GitHub repository.
2. **Add New Repository Secrets**:
   - `FTP_USERNAME`: Your InfinityFree FTP username.
   - `FTP_PASSWORD`: Your InfinityFree FTP password.
3. These secrets are referenced in the workflow file as `${{ secrets.FTP_USERNAME }}` and `${{ secrets.FTP_PASSWORD }}`, keeping your credentials secure.



## 6. Triggering the Workflow

1. **Push Changes to the `main` Branch**:
   - Make changes to your code and commit them to the `main` branch.
   - Push the changes to GitHub.

2. **Monitor GitHub Actions**:
   - Go to the **Actions** tab in your GitHub repository.
   - Select the running workflow to view logs, errors, and deployment status.



## 7. Verifying the Deployment

1. **Access Your Website**:
   - After the deployment completes, navigate to your website (e.g., `https://yourdomain.com`).
2. **Confirm Updates**:
   - Verify that the latest code changes are reflected on the live site.



## 8. Troubleshooting Common Issues

### Deployment Failed due to Incorrect Directory

Ensure that the `server-dir` path in the workflow matches the correct directory on InfinityFree. For the primary domain, it’s usually `/yourdomain.com/htdocs/`.

### Invalid FTP Credentials

If your deployment fails due to invalid credentials, double-check that your FTP username and password are correctly set up in GitHub Secrets and that they match those in InfinityFree.

### FTP Timeout or Rate Limit

InfinityFree may have restrictions on FTP access. If you encounter timeout issues, try deploying during non-peak hours or reducing the frequency of your deployments.



## Conclusion

With this setup, you have a streamlined CI/CD pipeline to deploy your website to InfinityFree via GitHub Actions. This automated process can save time, ensure consistency, and improve collaboration within your team.

**Happy Deploying!**