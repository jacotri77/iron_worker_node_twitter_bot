This is a basic Twitter bot, incorporating <a href="https://docs.docker.com">Docker</a> and <a href="http://dev.iron.io/worker/getting_started/">IronWorker</a> as a queue schedule example on the Iron.io IronWorker platform.

Credit to @amanhimself or  https://github.com/amandeepmittal for a lot of the base bot code.

Before starting make sure <a href="https://nodejs.org/en/download/package-manager/">node</a> is installed.  For this bot I used node 8.3. You can check if it is installed with either of these commands:

`node -v or
node --version`

If it is not installed run the following command to install it locally:

`brew install node`

This will also install `npm` which will enable you to add your other dependent packages.

Make sure you have the following dependencies installed:

`"dependencies": {
    "iron_mq": "^0.9.2",
    "iron_worker": "^0.1.6",
    "twit": "^2.2.9"
  }`

These can be installed using the following:<br />
`npm install iron_mq iron_worker twit`


This github assumes you have some familiarity with Docker and have a Docker Hub account.  If not, plese create one here:

https://hub.docker.com/

You will need to create a repository that you can build your images onto in the terminal. You can do so here:

https://cloud.docker.com/


`docker build -t USERNAME/IMAGENAME:TAG .`

You should see the following output in your terminal

`Sending build context to Docker daemon  9.867MB
Step 1/4 : FROM iron/node
 ---> 9ca501065d18
Step 2/4 : WORKDIR /app
 ---> Using cache
 ---> ad54579d14b1
Step 3/4 : ADD . /app
 ---> 23ca3c7f7d4e
Removing intermediate container 88bd966abbb8
Step 4/4 : ENTRYPOINT node bot.js
 ---> Running in cfce360ecfdd
 ---> de954b4b140b
Removing intermediate container cfce360ecfdd
Successfully built de954b4b140b
Successfully tagged erikjac/twitter:0.0.3`

`docker push USERNAME/IMAGENAME:TAG`

`The push refers to a repository [docker.io/erikjac/twitter]
a13329eaedc1: Layer already exists
2f159679b3bf: Layer already exists
0c9a3c45e098: Layer already exists
d578f3150db2: Layer already exists
d532ebb4a10d: Layer already exists
5b5d58ee6404: Layer already exists
0.0.3: digest: sha256:df41d5c718823eea7e06c2a49c676e5c6ef01cfadcc9e3f6cb2f17bf1`

`iron register USERNAME/IMAGENAME:TAG`

`Configuring client
        Project 'Twitter bot' with id='59b8769be92bfa000c182d8d'
----->  Registering worker 'erikjac/twitter'
        Registered code package with id='59baf777a74b24000a7240fa'
        Check https://hud-e.iron.io/worker/projects/59b8769be92bfa000c182d8d/code/59baf777a74b24000a7240fa for more info`

`iron worker upload -name bot USERNAME/IMAGENAME:TAG`

`Configuring client
        Project 'Twitter bot' with id='59b8769be92bfa000c182d8d'
----->  Uploading worker 'bot'
        Uploaded code package with id='59bafb21c5abcd000bafcd21'
        Check https://hud-e.iron.io/worker/projects/59b8769be92bfa000c182d8d/code/59bafb21c5abcd000bafcd21 for more info`

`iron worker queue bot`

`Configuring client
        Project 'Twitter bot' with id='59b8769be92bfa000c182d8d'
----->  Uploading worker 'bot'
        Uploaded code package with id='59bafb21c5abcd000bafcd21'
        Check https://hud-e.iron.io/worker/projects/59b8769be92bfa000c182d8d/code/59bafb21c5abcd000bafcd21 for more info`