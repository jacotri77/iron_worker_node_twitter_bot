This is a basic Twitter bot, incorporating <a href="https://docs.docker.com">Docker</a> and <a href="http://dev.iron.io/worker/getting_started/">IronWorker</a> as a queue schedule example on the Iron.io IronWorker platform.

Credit to @amanhimself or  https://github.com/amandeepmittal for a lot of the base bot code.

<h3>Before you begin</h3>

Before starting, you will need to setup a couple of things. You only need to do this once.

Get your app keys through <a href="https://apps.twitter.com/">Twitter</a><br> 
Install <a href="http://dev.iron.io/worker/cli/">Iron’s CLI tool</a><br />
Setup your <a href="http://dev.iron.io/worker/reference/configuration/">Iron.io credentials</a><br />
Install <a href="https://www.docker.com/get-docker">Docker</a><br />
Install <a href="https://nodejs.org/en/download/package-manager/">node</a>.  For this bot I used node 8.3. You can check if it is installed with either of these commands:

`node -v or
node --version`

If it is not installed run the following command to install it locally:

`brew install node`

This will also install `npm` which will enable you to add `yarn`, which I prefer for installing dependendcies as it is deterministic so this project isn't broken a year from now :).

`npm install -g yarn`

Make sure you have the following dependencies installed:

`"dependencies": {
    "iron_mq": "^0.9.2",
    "iron_worker": "^0.1.6",
    "twit": "^2.2.9"
  }`

These can be installed using the following:<br />
`yarn add iron_mq iron_worker twit`

Make sure you have your Iron.io credentials either referenced in your project or in a separate `iron.json` file (best practice).  You can set your credentials up and download the Iron CLI here:



This github assumes you have some familiarity with Docker and have a Docker Hub account.  If not, plese create one here:

https://hub.docker.com/

You will need to create a Docker repository here:

https://cloud.docker.com/

<h4>***Note: you can also create a repo through the <a href="https://docs.docker.com/engine/reference/commandline/create/">terminal</a>***</h4>

<h3>Getting your Twitter credentials added</h3><ul>
        <li>Create a directory to add your code into</li>
        <li>Create(or modify if you cloned down my repo) a file called <b>config.js</b></li>
        <li>Copy your Twitter credentials in as show below:<br><br>
                `module.exports = {<br>
                    consumer_key: '',<br>  
                    consumer_secret: '',<br>
                    access_token: '', <br> 
                    access_token_secret: ''<br>
                    }`</li></ul><br>

<h3>Let's get the code uploaded and running in IronWorker!</h3><b2>
<h4>1. Package your Worker</h4><br>

Let’s package it up inside a Docker image and upload it to a Docker Registry. Copy the Dockerfile from appropriate directory (depending on used programming language) of this <a href="https://github.com/iron-io/dockerworker">repository</a> and modify the ENTRYPOINT (in this case we are using bot.js) line to run your script. Build your docker image:<br><br>
`docker build -t USERNAME/IMAGENAME:TAG .` <br><br>
Replace USERNAME/IMAGENAME with your own Docker info or you can also build off of the lightwieght images iron has for each language (iron/node, iron/go, etc.).  Those can be accessed <a href="https://github.com/iron-io/dockerworker">here</a>

You should see the following output(with a few semantic differences based on your naming scheme and image size) in your terminal

`Sending build context to Docker daemon  9.867MB `<br />
`Step 1/4 : FROM iron/node`<br />
 `---> 9ca501065d18`<br />
`Step 2/4 : WORKDIR /app`<br />
 `---> Using cache`<br />
 `---> ad54579d14b1`<br />
`Step 3/4 : ADD . /app`<br />
 `---> 23ca3c7f7d4e`<br />
`Removing intermediate container 88bd966abbb8`<br />
`Step 4/4 : ENTRYPOINT node bot.js`<br />
 `---> Running in cfce360ecfdd`<br />
 `---> de954b4b140b`<br />
`Removing intermediate container cfce360ecfdd`<br />
`Successfully built de954b4b140b`<br />
`Successfully tagged USERNAME/IMAGENAME:TAG`<br />

<h4>2. Push it to Docker Hub</h4>

Push it to Docker Hub:

```docker push USERNAME/IMAGENAME:TAG```

```
The push refers to a repository [docker.io/USERNAME/IMAGENAME]
a13329eaedc1: Layer already exists
2f159679b3bf: Layer already exists
0c9a3c45e098: Layer already exists
d578f3150db2: Layer already exists
d532ebb4a10d: Layer already exists
5b5d58ee6404: Layer already exists
0.0.3: digest: sha256:df41d5c718823eea7e06c2a49c676e5c6ef01cfadcc9e3f6cb2f17bf1
```

<h4>3. Register your image with Iron</h4>

Ok, we’re ready to run this on Iron now, but first we have to let Iron know about the image you just pushed to Docker Hub.<br><br>
`iron register USERNAME/IMAGENAME:TAG`

`Configuring client`<br />
        `Project 'Twitter bot' with id='59b8769be92bfa000c182d8d'`<br />
`----->  Registering worker 'USERNAME/IMAGENAME'`<br />
        `Registered code package with id='59baf777a74b24000a7240fa'`<br />
        `Check `<br />`https://hud-e.iron.io/worker/projects/59b8769be92bfa000c182d8d/code/59baf77a74b24000a7240fa for more info`<br />

<h4>3. Upload the worker code package to IronWorker</h4>

This uplaods the code package you are going to use in your worker to IronWorker.<br><br>
`iron worker upload -name bot USERNAME/IMAGENAME:TAG`

`Configuring client`<br />
        `Project 'Twitter bot' with id='59b8769be92bfa000c182d8d'`<br />
`----->  Uploading worker 'bot'`<br />
        `Uploaded code package with id='59bafb21c5abcd000bafcd21'`<br />
        `Check `<br />`https://hud-e.iron.io/worker/projects/59b8769be92bfa000c182d8d/code/59bafb21c5abcd000bafcd21 for more info`

<h4>5. Queue / Schedule jobs for your image</h4>

Now you can start queuing jobs or schedule recurring jobs for your image.<br><br>
`iron worker queue bot` for a direct queue<br> You can schedule your task `iron worker queue bot --start-at "12:30" --run-times 5 --run-every 70`

`Configuring client`<br />
        `Project 'Twitter bot' with id='59b8769be92bfa000c182d8d'`<br />
`----->  Uploading worker 'bot'`<br />
        `Uploaded code package with id='59bafb21c5abcd000bafcd21'`<br />
        `Check` <br />`https://hud-e.iron.io/worker/projects/59b8769be92bfa000c182d8d/code/59bafb21c5abcd000bafcd21 for more info`

<h4>6. Check on your running or finished tasks</h4>

Look at <a href="https://hud-e.iron.io">HUD</a> to view your scheduled tasks, running or completed tasks, check logs, etc.<br><br>
