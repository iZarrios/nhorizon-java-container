## Running the app
```bash
$ npm install # install dependencies
$ node app.js
```
## Building Docker image
```bash
$ docker build . -t name:tag
```

## Pushing Docker image to Docker Hub
```bash
$ docker push name:tag
```

## Running Docker image
```bash
$ docker run -p 9090:9090 name:tag
```

## Docs:
- Tried to run the app without Docker, but with node.js and npm installed on my machine
- Modified the home.pug file to include group code as well as my name
- Tried to run the app with Docker and it didn't work at first
    - After figuring out the correct tag for the image, it worked
- Pushed the image to Docker Hub, it didn't work at first, but after a few tries it worked
    - I had to log in to Docker Hub in the terminal
- Added template Jenkinsfile to the repository
- I added Jenkins to Docker group because I was getting permission denied error when trying to run the Jenkins container because it was trying to access the Docker socket
- Tried running the Jenkins container with the Docker socket mounted and it worked after adding Jenkins to Docker group
- Created a new pipeline in Jenkins and configured it to use the Jenkinsfile in the repository
- Ran the pipeline and I had some problems with the Docker image not being able to be pushed to Docker Hub
    - I had to log in to Docker Hub in the Jenkins container
    - I used the credentials plugin to store the Docker Hub credentials
- Ran the pipeline again and it did not work as I was testing the pipeline with a wrong port
- Fixed the port in the Jenkinsfile and ran the pipeline again
- The pipeline ran successfully and the image was pushed to Docker Hub
- The pipeline had a stage where it tried pinging the app, but it failed
    - I had to change the port in the Jenkinsfile to 9090
- Ran the pipeline again and it was successful


## Adding a remote node to Jenkins
### 1. Prepare the Remote Machine
Before configuring the node in Jenkins, ensure the remote machine (where the node will run) is prepared:
Install Java (Jenkins requires Java to run the agent).
You can check if Java is installed with java -version. If not, install it:
```bash
$ sudo apt-get update
$ sudo apt-get install openjdk-11-jre # Java 8 or higher is typically required, depending on your Jenkins version.
```
Ensure the machine is network-accessible from the Jenkins master (basically make sure you can ping the machine from the Jenkins server).

### 2. Adding a New Node in Jenkins

#### On the Jenkins master, follow these steps:

1. **Go to Manage Jenkins**:
   From the Jenkins dashboard, click on `Manage Jenkins` from the left-hand menu.

2. **Go to Manage Nodes and Clouds**:
   Under `Manage Jenkins`, click on `Manage Nodes and Clouds`.

3. **Add a New Node**:
   Click `New Node` on the left-hand side.

4. **Enter Node Details**:
   Give your node a name (e.g., `remote-node`).

5. **Select "Permanent Agent"**:
   If you're setting up a static agent (as opposed to a cloud-based or ephemeral one), select **Permanent Agent** and click `OK`.

6. **Configure the Node**:
   On the next page, you'll need to fill out the details:
   - **Description**: A short description of the node (optional).
   - **# of Executors**: The number of parallel builds the node can handle (usually 1).
   - **Remote root directory**: The directory on the remote machine where the Jenkins agent will be installed and work (e.g., `$HOME`).
   - **Labels**: Add a label (e.g., `remote-node`) for identifying this node in your pipeline.
   - **Launch method**: Choose one of the following methods to launch the agent:
     - **Launch agents via SSH**: Provide SSH credentials to connect the agent to the remote machine.
     - **Launch agents via Java Web Start** **_(not needed in our case)_**: Manually start the agent from the remote machine using a `.jnlp` file.

### 3. Configure SSH for Agent (if using SSH launch method)

If you selected **Launch agent via SSH**, follow these steps to set up SSH access:

1. **Configure SSH Key**:
   You’ll need to configure SSH access from Jenkins to the remote machine. Use an SSH key pair (passwordless login) for this.

   To generate an SSH key (if you don't have one), run the following command on the Jenkins master machine:

   ```bash
   $ ssh-keygen -t rsa -b 2048
   ```
2. **Copy the Public Key**:
   Copy the public key to the remote machine. You can do this manually or use `ssh-copy-id`:

   ```bash
    $ ssh-copy-id jenkins@<remote-node-ip>
   ```
   Replace <remote-node-ip> with the actual IP address or hostname of the remote machine.
3. **Add SSH Credentials to Jenkins**:
   In Jenkins, go to `Manage Jenkins` > `Manage Credentials` > `Jenkins` > `Global credentials (unrestricted)` > `Add Credentials`.

   Choose `SSH Username with private key` as the kind of credentials and fill in the details:
   - **Username**: The username for the remote machine (e.g., `jenkins`).
   - **Private Key**: Enter directly or select the private key file.
   - **Passphrase**: If your SSH key has a passphrase, enter it here.
4. **Start the Agent**:
   Once you've configured the node in Jenkins and set up SSH access, you can start the agent by clicking on the node and selecting `Launch agent`.
5. **Verify Connection**:
   After launching the agent, Jenkins will attempt to connect to the remote machine. You can check the status and logs to verify the connection.
