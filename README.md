## Running the app
```bash
npm install # install dependencies
node app.js
```
## Building Docker image
```bash
docker build . -t name:tag
```

## Pushgin Docker image to Docker Hub
```bash
docker push name:tag
```

## Running Docker image
```bash
docker run -p 9090:9090 name:tag
```

Docs:
- Tried to run the app without Docker, but with node.js and npm installed on my machine
- Modified the home.pug file to include group code as well as my name
- Tried to run the app with Docker and it didn't work at first
    - After figuring out the correct tag for the image, it worked
- Pushed the image to Docker Hub, it didn't work at first, but after a few tries it worked
    - I had to log in to Docker Hub in the terminal
- Added template Jenkinsfile to the repository
- I added Jenkins to Docker group because I was getting permission denied error when trying to run the Jenkins container because it was trying to access the Docker socket
- Tried running the the Jenkins container with the Docker socket mounted and it worked after adding Jenkins to Docker group
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

