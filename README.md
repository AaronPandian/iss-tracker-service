# Flask ISS Trajectory Analysis Service

### High-Level Description
This repository contains the source code and instructions to build, run, and test a developed containerized flask application. The service relays current information on the International Space Station (ISS) trajectory to the user. The methods of engaging with this data can be found in the various flask routes detailed below. The ephemeris data the service utilizes is from NASA's public ISS trajectory dataset [[1]](#citations). 

### Table of Contents
1. [Software Diagram](#software-diagram)
2. [Data Description](#data-description)
3. [Build and Deploy](#build-and-deploy)
   1. [How to Build the Container](#how-to-build-the-container)
   2. [How to Deploy Containerized Code as a Flask App](#how-to-deploy-containerized-code-as-a-flask-app)
   3. [How to Run Unit Tests](#how-to-run-unit-tests)
4. [Service Functionality](#service-functionality)
   1. [Accessing Routes](#accessing-routes)
   2. [What Outputs to Expect](#what-outputs-to-expect)
5. [Citations](#citations)

### Software Diagram
![Alt text](https://github.com/AaronPandian/coe323-homeworks/blob/main/homework05/diagram.png)

### Data Description
The ISS tracking data this app requests can be found on the NASA website [[1]](#citations). This ephemeris dataset, compiled by the NASA Johnson Space Center, contains a header section and a primary data section. The header contains the ISS mass in kg, drag area in m<sup>2</sup>, and drag coefficient used in generating the subsequent data. The primary data section contains information from the last 15-day interval. The timesteps vary from 4 minutes to 2 seconds and timestep notes state vectors detailing the time in UTC ISO date format; position X, Y, and Z in km; and velocity X, Y, and Z in km/s.

### Build and Deploy
First, ensure the environment you are using has Docker installed. Second, you should be conducting the following within the root folder you imported the source code into before.
#### How to Build the Container
Create a folder in your directory to input the code, for example, "iss_app", by running `mkdir iss_app`.

Run `cd iss_app` to enter the created folder then run the `wget <linktofile>` command to import all the files from this repository into your directory. 

Once all the files are gathered, double-check with `ls`. To build the image, run the command `docker build -t <dockerhubusername>/iss_tracker:1.0 .` and check if the build was successful with `docker images`.

By implementing the command above, you should see the image created with the tag `<dockerhubusername>/iss_tracker:1.0`.

#### How to Deploy Containerized Code as a Flask App
After building the image, you can run the instance as a container. To do so, enter the command `docker-compose up -d`. This runs the Docker Compose file which deploys the image in the background.  

At this point, your container is running the main iss_tracker.py script in the background of your terminal. Use the following section to interact with the application. 

Once you are done running the Flask app, to clean up your interface, remove the image using the container ID found when running `docker images`. Once the ID is found, run `docker stop <containerID>` to stop the application from running in the background, and then `docker rm <containerID>` to remove the instance from your list of images.

#### How to Run Unit Tests
Just to note, you can run a unit test script to ensure the main iss_tracker.py script is running as it should. After the image is built and while the main script is not running, use the `docker run <dockerhubusername>/iss_tracker:1.0 test_iss_tracker.py` command to run the test. If no output is seen, then the main service script is working as it should be.  

### Service Functionality
#### Accessing Routes
After running the `docker-compose up -d` command, a background terminal will be waiting for requests to be made using specific URL routes. Using the HTTPS URL displayed in your main terminal, type `curl <URL>`, then append the following routes at the end of the URL to induce the desired dataset analysis. 

* `/epochs` returns the whole dataset.
* `/epochs?limit=int&offset=int` returns a modified dataset given the integer query parameters. Note that to run this route, specifically, the command will include quotation marks like `curl '<URL>/epochs?limit=int&offset=int'`.
     * The offset parameter denotes the number of epochs you want to return and the limit sets the final epoch index. If the offset value is greater than the limit, _all_ preceding epoch data points will be returned. 
* `/epochs/<epoch>` returns the state vector information for the specific epoch at index `<epoch>`.
     * The `<epoch>` entered into the route will throw an error if this value is not an integer.
* `/epochs/<epoch>/speed` returns the instantaneous speed of the ISS at the specified epoch at index `<epoch>`.
     * The `<epoch>` entered into the route will throw an error if this value is not an integer.
* `/now` returns the instantaneous speed for the latest epoch alongside the current latitude, longitude, altitude, and geoposition- in order.
* `/comment` returns the comments from the ISS ephemeris dataset.
* `/header` returns the header information, as detailed prior, from the ISS ephemeris dataset.
* `/metadata` returns the metadata from the ISS ephemeris dataset.
     * This information includes the ISS object name, ID, center name, data reference frame, time system, start time, and end time. 

#### What Outputs to Expect
In running the main script from an image, once running the routes above, the user should receive the respective information printed out to the terminal. 

### Citations
<a id="1">[1]</a>
“Spot the Station.” ISS Trajectory Data, NASA, spotthestation.nasa.gov/trajectory_data.cfm. Accessed 6 Mar. 2024. 
