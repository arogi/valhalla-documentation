<b>Roll your own geospatial routing engine!</b>  
<i>Customize and build Mapzen Valhalla, then deploy with Docker</i>  

A. Customize your configuration

1. Fork the valhalla/conf repo.

2. Edit valhalla.json with your preferences. For example, the standard Valhalla matrix configuration `"sources to targets"`possesses a default limit of 50 nodes.

B. Prepare valhalla-docker repo

1. Fork the valhalla/valhalla-docker repo on GitHub and note the new location of the repo in your account. Clone onto your local machine. Typically, the terminal command to clone would be something like: `git clone https://github.com/yourusername/valhalla-docker.git`

2. Using the GitHub workflow (branch/edit/pull request), edit your local version of Dockerfile. Using a # to comment out the line:
`# RUN wget https://s3.amazonaws.com/metro-extracts.mapzen.com/trento_italy.osm.pbf`

3. Add the following line soon after the line "ENV TERM xterm":  
`COPY *.pbf /`

4. Point to your own configuration file instead of Mapzen Valhalla's default. Change the line:
    `--branch=master https://github.com/valhalla/conf.git`
	to
    `--branch=master https://github.com/arogi/conf.git`

5. Create a .gitignore file with the following contents:  
`conf`<br />
`*.pbf`

5b. Obtain and place your protobuffer data into your repo folder.

6. Edit build.sh to change the image tag name, from:
  `--tag wouldgo/valhalla \`
	to
  `--tag arogi/valhalla \`

7. Edit run-valhalla.sh to refer to this new tag (e.g. wouldgo/valhalla to arogi/valhalla).

C. Building

The building process occurs on your local machine. The changes you have made to valhalla.json, Dockerfile, build.sh, and source data will allow you to create a Docker image of Valhalla using your own custom region of interest, with your custom configuration.
The build command will take a long time to run--varying by amount of input data and your computer's specs. Upon successful completion, you will have a Docker image with the name arogi/valhalla

1. To build, type:
`./build.sh`


D. Testing

1. The following command will run a container with the Valhalla build:
`docker run -p 8002:8002 arogi/valhalla`

2. Depending on your OS, type:  
Mac/Windows: `http://192.168.99.100:8002`  
Linux: `localhost:8002`

If successful, Valhalla will respond with a statement such as: `/route /many_to_many /location`

An example of a more realistic test (would require California to be used as input data):
`http://192.168.99.100:8002/route?json={%22locations%22:[{%22lat%22:33.896637,%22lon%22:-118.298034},{%22lat%22:34.076549,%22lon%22:-117.920379}],%22costing%22:%22auto%22,%22units%22:%22km%22}`
