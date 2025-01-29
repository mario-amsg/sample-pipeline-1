# Setting Up the Git Repository and Deploying the App to Minikube

This guide provides step-by-step instructions for setting up the Git repository on the Git VM and deploying a sample Python application to the Minikube VM.

---

## 1. Setting Up the Git Repository

### Initialize the Repository
1. SSH into the Git VM:
   ```bash
   vagrant ssh git
   ```
2. Create a directory for the repository:
   ```bash
   mkdir -p /home/vagrant/repo
   cd /home/vagrant/<repo-name>
   git init --bare
   ```
   This initializes a bare repository for remote access.

### Configure SSH Access (Optional)
If using SSH for Git access:
1. Generate an SSH key pair on the Jenkins VM:
   ```bash
   ssh-keygen -t ed25519
   ```
2. Copy the public key to the Git VM:
   ```bash
   ssh-copy-id -i ~/.ssh/id_ed25519.pub vagrant@git.local
   ```
   ***NOTE***:
   Use the password "vagrant" when prompted

3. Verify SSH access:
   ```bash
   ssh vagrant@git.local
   ```

4. Repeat these steps on the host if you want remote access from your local machine using SSH keys - Recommended as you will be pushing changes to the remote repository later

### Configure HTTP Access (Optional)
If using HTTP for Git access:
1. Install Apache and Git on the Git VM:
   ```bash
   sudo apt-get install -y apache2 git
   sudo systemctl start apache2
   ```
2. Create a bare repository for HTTP access:
   ```bash
   sudo mkdir -p /var/www/html/repo.git
   sudo git init --bare /var/www/html/repo.git
   sudo chown -R www-data:www-data /var/www/html/repo.git
   sudo chmod -R 775 /var/www/html/repo.git
   ```
3. Enable directory listing in Apache:
   ```bash
   echo "Options +Indexes" | sudo tee /var/www/html/.htaccess
   sudo systemctl restart apache2
   ```
4. Access the repository via HTTP:
   ```plaintext
   http://git.local/repo.git
   ```

### Push Initial Code to the Repository
1. On your host machine or another client, initialize a local repository:
   ```bash
   git init
   ```
2. Add the remote repository:
   ```bash
   git remote add origin vagrant@git.local:/home/vagrant/<repo-name>
   ```
   Or for HTTP:
   ```bash
   git remote add origin http://git.local/repo.git
   ```
3. Add your application code and push it:
   ```bash
   git add .
   git commit -m "Initial commit"
   git push origin main
   ```

   ***NOTE***:
   In this step, you can copy the files under the "src" directory to the new repository 

---

## 2. Deploying the Application to Minikube

### Verify Minikube is Running
1. SSH into the Minikube VM:
   ```bash
   vagrant ssh minikube
   ```
2. Start Minikube:
   ```bash
   minikube start
   ```
   This ensures Minikube is running and accessible.

### Configure Jenkins Pipeline
1. Access Jenkins

    Open Jenkins in your browser: http://jenkins.local:8080
    Log in using your admin credentials.

2. Create a New Pipeline Job

    On the Jenkins dashboard, click New Item.
    Enter a name for the job (e.g., Deploy to Minikube).
    Select Pipeline as the project type.
    Click OK.

3. Configure the Pipeline

    In the Pipeline section:
        Choose Pipeline script from SCM.
        In the SCM dropdown, select Git.
        Enter the URL of your repository where the Jenkinsfile is stored.
            Example: vagrant@git.local:/home/vagrant/app.git
        Specify the branch (e.g., main).
        If the repository is private, add your credentials by clicking Add under Credentials.
    Script Path:
        If your Jenkinsfile is in the root of your repository, leave this as Jenkinsfile.
        If it’s in a subdirectory, specify the relative path (e.g., ci/Jenkinsfile).

4. Save and Run

    Click Save to store the job configuration.
    Run the pipeline:
        Go to the job dashboard.
        Click Build Now.
        Jenkins will pull the repository, execute the pipeline steps, and deploy the application.

5. Monitor the Pipeline

    Open the Build Console Output to track progress and troubleshoot any issues.
    Once the pipeline finishes successfully, verify the deployment on Minikube.

### Apply Kubernetes Deployment
1. On the Minikube VM, create the deployment and service using `deployment.yaml`:
   ```bash
   kubectl apply -f deployment.yaml
   ```

2. Verify the deployment:
   ```bash
   kubectl get pods
   kubectl get services
   ```

### Access the Application
1. List the Minikube service to find the exposed NodePort:
   ```bash
   minikube service list
   ```
2. Open the application in your browser using Minikube’s IP and the NodePort.
   ```
   http://<minikube-ip>:<node-port>
   ```

---

## Troubleshooting

### Git Repository Issues
- Verify the repository is initialized as a **bare repository**.
- Ensure SSH keys are correctly configured if using SSH.

### Jenkins Pipeline Errors
- Check the console output for specific error messages.
- Verify that Jenkins has access to the repository.

### Kubernetes Deployment Issues
- Ensure `kubectl` is configured to use Minikube’s context:
  ```bash
  kubectl config use-context minikube
  ```
- Check the pod logs for errors:
  ```bash
  kubectl logs <pod-name>
  ```

---

You’re all set! Follow these steps to deploy your Python application successfully. Reach out for help if you get stuck. 🚀
