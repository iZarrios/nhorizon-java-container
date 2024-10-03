docker build . -t name:tag
docker push name:tag
and you are good to go ;)

Docs:
- Tried to run the app without Docker, but with node.js and npm installed on my machine
- Modified the home.pug file to include group code as well as my name
- Tried to run the app with Docker and it didn't work at first
    - After figuring out the correct tag for the image, it worked
- Pushed the image to Docker Hub, it didn't work at first, but after a few tries it worked
    - I had to log in to Docker Hub in the terminal
- Added template Jenkinsfile to the repository
- I added Jenkins to Docker group because I was getting permission denied error when trying to run the Jenkins container because it was trying to access the Docker socket
