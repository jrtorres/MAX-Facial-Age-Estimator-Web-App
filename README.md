# Create a web app to show the age estimation from the detected human faces

The biological ages of people oftentimes provide a significant information for many applications; for example, surveillance, product recommendation, etc.
Existing commercial devices such as mobile phones, webcams are used to create visual data (images/videos) constantly. Given visual data with human faces,
the facial age estimator model predicts the ages of the detected faces. With the predicted ages, the information can be applied to different algorithms
such as "grouping" which provides the observations in statistics --- different groups of people for various activities.

In this Code Pattern we will use one of the models from the
[Model Asset Exchange (MAX)](https://developer.ibm.com/exchanges/models/),
an exchange where developers can find and experiment with open source deep learning
models. Specifically we will be using the [Facial Age Estimator](https://developer.ibm.com/exchanges/models/all/max-facial-age-estimator/)
to create a web application that will first detect human faces and then output the ages with the bounding boxes
of the associated detected faces. The web application provides an user-friendly interface
backed by a lightweight python server. The server takes webcam as input via the
UI and sends them to a REST end point for the model. The model's REST endpoint is set up using the docker image
provided on MAX. The Web UI displays the estimated age with the associated bounding box for each person.

When the reader has completed this Code Pattern, they will understand how to:

* Build a Docker image of the Facial Age Estimator MAX Model
* Deploy a deep learning model with a REST endpoint
* Generate the estimated ages for an image using the MAX Model's REST API
* Run a web application that using the model's REST API

![Architecture](doc/source/images/architecture.png)

## Flow

1. Server sends the captured video frame-by-frame from webcam to Model API.
2. Web UI requests the age and bounding box data for frame(s) from Server.
3. Server receives data from Model API and updates the result to Web UI.


## Included Components

* [IBM Model Asset Exchange](https://developer.ibm.com/exchanges/models/): A place for developers to find and use
free and open source deep learning models.
* [Docker](https://www.docker.com): Docker is a tool designed to make it easier to create, deploy, and run applications
by using containers.

## Featured Technologies

* [Python](https://www.python.org/): Python is a programming language that lets you work more quickly and integrate
your systems more effectively.
* [JQuery](https://jquery.com): jQuery is a cross-platform JavaScript library designed to simplify the client-side
scripting of HTML.
* [Bootstrap 3](https://getbootstrap.com): Bootstrap is a free and open-source front-end library for designing websites
and web applications.

### Setting up the MAX Model

> NOTE: The set of instructions in this section are a modified version of the one found on the
[Facial Age Estimator Project Page](https://github.com/IBM/MAX-Facial-Age-Estimator)

#### Deploy the Model

1. To run the docker image, which automatically starts the model serving API, run:

   ```bash
    docker run -it -p 5000:5000 -p 7000:7000 --name=max-facial-age-estimator codait/max-facial-age-estimator
   ```

   This will pull a pre-built image from Docker Hub (or use an existing image if already cached locally) and run it. To run the web app with Docker the containers running the model serving API and the web app need to share the same network stack. This is done by mapping an additional port in the container to a port on the host machine. In the example above it is mapped to port `7000` on the host but other ports can also be used.If you'd rather build the model locally you can follow the steps in the[model README](https://github.com/IBM/MAX-Facial-Age-Estimator/blob/master/README.md#steps).

   _Note_ that currently this docker image is CPU only (we will add support for GPU images later).

#### (Optional) Experimenting with the API

1. The API server automatically generates an interactive Swagger documentation page.
Go to `http://localhost:5000` to load it. From there you see the API with the test requests.

   Use the `model/predict` endpoint to load a test file and get estimated ages and bounding boxes for the image from the API.

1. The [model assets folder](https://github.com/IBM/MAX-Facial-Age-Estimator/tree/master/assets)
contains one image you can use to test out the API, or you can use your own.

1. You can also test it on the command line, for example:

   ```bash
   curl -F "image=@path/to/tom_cruise.jpg" -X POST http://localhost:5000/model/predict
   ```

   ```json
   {
   "status": "Okay",
   "predictions": [
    {
      "age_estimation": [
        48
      ],
      "face_box": [
        "303",
        "174",
        "379",
        "515"
      ]
   }
   ]
   }
   ```

### Run the Web App using Docker

#### Check out the code

1. Clone the Age Estimation Web App repository locally by running the following command:

   ```bash
    git clone https://github.com/jrtorres/MAX-Facial-Age-Estimator-Web-App.git
   ```

1. Then change directory into the local repository

   ```bash
    cd MAX-Facial-Age-Estimator-Web-App
   ```

#### Build and Run the Web App Container

1. Build the web app image by running:

   ```bash
    docker build -t max-age-estimator-web-app .
   ```

1. Run the web app container using:

   ```bash
    docker run -it --net='container:max-facial-age-estimator' max-age-estimator-web-app
   ```

1. Use a browser to visit the application at [`http://localhost:7000`](http://localhost:7000)

### (Optional) Run the Web App using Python

If you have Python installed locally, you can run the web application component locally.

1. If you have not cloned the age estimation web application repository locally yet, run the following:

   ```bash
    git clone https://github.com/jrtorres/MAX-Facial-Age-Estimator-Web-App.git
   ```

1. Then change directory into the local repository

   ```bash
    cd MAX-Facial-Age-Estimator-Web-App
   ```

1. The general recommendation for Python development is to use a virtual environment [(venv)](https://docs.python.org/3/tutorial/venv.html). To install and initialize a virtual environment, use the `venv` module on Python 3:

   ```bash
   # Create the virtual environment using Python.
   # Note, it may be named python3 on your system.

   $ python -m venv mytestenv       # Python 3.X

   # Now source the virtual environment. Use one of the two commands depending on your OS.

   $ source mytestenv/bin/activate  # Mac or Linux
   $ ./mytestenv/Scripts/activate   # Windows PowerShell
   ```

1. Before running this web app you must install its dependencies:

   ```bash
   pip install -r requirements.txt
   ```

   > **TIP** :bulb: To terminate the virtual environment use the `deactivate` command.

1. You then start the web app by running:

   ```bash
    python app.py
   ```

1. You can then access the web app at: [`http://localhost:7000`](http://localhost:7000)

# Links

* [Model Asset eXchange (MAX)](https://developer.ibm.com/exchanges/models/)
* [Center for Open-Source Data & AI Technologies (CODAIT)](https://developer.ibm.com/code/open/centers/codait/)
* [MAX Announcement Blog](https://developer.ibm.com/code/2018/03/20/igniting-a-community-around-deep-learning-models-with-model-asset-exchange-max/)

# Learn More

* **Artificial Intelligence Code Patterns**: Enjoyed this Code Pattern? Check out our other
[Artificial Intelligence Code Patterns](https://developer.ibm.com/technologies/artificial-intelligence/)
* **AI and Data Code Pattern Playlist**: Bookmark our
[playlist](https://www.youtube.com/playlist?list=PLzUbsvIyrNfknNewObx5N7uGZ5FKH0Fde) with all of our Code Pattern videos
* **Watson Studio**: Master the art of data science with IBM's [Watson Studio](https://dataplatform.cloud.ibm.com/)
* **Deep Learning with Watson Studio**: Design and deploy deep learning models using neural networks, easily scale to
hundreds of training runs. Learn more at [Deep Learning with Watson Studio](https://www.ibm.com/cloud/deep-learning).

# License
This code pattern is licensed under the Apache Software License, Version 2.  Separate third party code objects invoked within this code pattern are licensed by their respective providers pursuant to their own separate licenses. Contributions are subject to the [Developer Certificate of Origin, Version 1.1 (DCO)](https://developercertificate.org/) and the [Apache Software License, Version 2](https://www.apache.org/licenses/LICENSE-2.0.txt).

[Apache Software License (ASL) FAQ](https://www.apache.org/foundation/license-faq.html#WhatDoesItMEAN)
