## Objective

1. Use **FastAPI** to create an API for your model
2. Run that API on your machine
3. Put it in production

## Context

Now that we have a performant model trained in the cloud, we will expose it to the world 🌍

We will create a **prediction API** for our model, run it on our machine to make sure that everything works correctly, and then we will deploy it in the cloud so that everyone can play with our model!

To do so, we will:

👉 create a **prediction API** using **FastAPI**

👉 create a **Docker image** containing the environment required to run the code of our API

👉 push this image to **Google Cloud Run** so that it runs inside a **Docker container** that will allow developers all over the world to use it

# 1️⃣ Project Setup 🛠

<details>
  <summary markdown='span'><strong>❓Instructions </strong></summary>

## Environment

Copy your `.env` file from the previous package version:

```bash
cp ~/code/<user.github_nickname>/{{local_path_to('07-ML-Ops/03-Automate-model-lifecycle/01-Automate-model-lifecycle')}}/.env .env
```

OR

Use the provided `env.sample`, replacing the environment variable values with yours.

## API Directory

A new `taxifare/api` directory has been added to the project to contain the code of the API along with 2 new configuration files, which can be found in your project's root directory:

```bash
.
├── Dockerfile          # 🎁 NEW: building instructions
├── Makefile            # good old manual task manager
├── README.md
├── requirements.txt    # all the dependencies you need to run the package
├── setup.py
├── taxifare
│   ├── api             # 🎁 NEW: API directory
│   │   ├── __init__.py
│   │   └── fast.py     # 🎁 NEW: where the API lives
│   ├── interface       # package entry point
│   └── ml_logic
└── tests
```

Now, have a look at the `requirements.txt`. You can see newcomers:

``` bash
# API
fastapi         # API framework
pytz            # time zone management
uvicorn         # web server
# tests
httpx           # HTTP client
pytest-asyncio  # asynchronous I/O support for pytest
```

⚠️ Make sure to perform a **clean installation** of the package.

<details>
  <summary markdown='span'>❓How?</summary>

`make reinstall_package`, of course 😉

</details>

## Running the API with FastAPI and a Uvicorn Server

We provide you with a FastAPI skeleton in the `fast.py` file.

**💻 Try to launch the API now!**

<details>
  <summary markdown='span'>💡 Hint</summary>

You probably want a `uvicorn` web server with 🔥 hot-reloading...

In case you can't find the proper syntax, and look at your `Makefile`; we provided you with a new task: `run_api`.

If you run into the error `Address already in use`, the port `8000` on your local machine might already be occupied by another application.

You can check this by running `lsof -i :8000`. If the command returns something, then port `8000` is already in use.

In this case, specify another port in the [0, 65535] range in the `run_api` command using the `--port` parameter.
</details>

**❓ How do you consult your running API?**

<details>
  <summary markdown='span'>Answer</summary>

💡 Your API is available locally on port `8000`, unless otherwise specified 👉 [http://localhost:8000](http://localhost:8000).
Go visit it!

</details>

You have probably not seen much...yet!

**❓ Which endpoints are available?**

<details>
  <summary markdown='span'>Answer</summary>

There is only one endpoint (_partially_) implemented at the moment, the root endpoint `/`.
The "unimplemented" root page is a little raw, but remember that you can always find more info on the API using the Swagger endpoint 👉 [http://localhost:8000/docs](http://localhost:8000/docs)

</details>

</details>


# 2️⃣  Build the API 📡

<details>
  <summary markdown='span'><strong>❓Instructions </strong></summary>
An API is defined by its specifications (see [GitHub repositories API](https://docs.github.com/en/rest/repos/repos)). Below you will find the API specifications you need to implement.

## Specifications

### Root

- Denoted by the `/` character
- HTTP verb: `GET`

In order to easily test your `root` endpoint, use the following response example as a goal:
```json
{
    'greeting': 'Hello'
}
```

- 💻 Implement the **`root`** endpoint `/`
- 👀 Look at your browser 👉 **[http://localhost:8000](http://localhost:8000)**
- 🐛 Inspect the server logs and, if needed, add some **`breakpoint()`s** to debug

When and **only when** your API responds as required:
1. 🧪 Test your implementation with `make test_api_root`
2. 🧪 Track your progress on Kitt with  `make test_kitt` & push your code!

### Prediction

- Denoted by `/predict`
- HTTP verb: `GET`

It should accepts the following query parameters

<br>

| Name | Type | Sample |
|---|---|---|
| pickup_datetime | DateTime | `2013-07-06 17:18:00` |
| pickup_longitude | float | `-73.950655` |
| pickup_latitude | float | `40.783282` |
| dropoff_longitude | float | `-73.950655` |
| dropoff_latitude | float | `40.783282` |
| passenger_count | int | `2` |

<br>

It should return the following JSON
```json
{
    'fare_amount': 5.93
}
```

**❓ How would you proceed to implement the `/predict` endpoint? Discuss with your buddy 💬**

Ask yourselves the following questions:
- How should we build `X_pred`? How to handle timezones ?
- How can we reuse the `taxifare` model package in the most lightweight way ?
- How to render the correct response?

<details>
  <summary markdown='span'>💡 Hints</summary>

- Re-use the methods available in the `taxifare/ml_logic` package rather than the main routes in `taxifare/interface`; always load the minimum amount of code possible!

</details>


👀 Inspect the **response** in your **browser**, and inspect the **server logs** while you're at it

👉 Call on your browser [http://localhost:8000/predict?pickup_datetime=2014-07-06%2019:18:00&pickup_longitude=-73.950655&pickup_latitude=40.783282&dropoff_longitude=-73.984365&dropoff_latitude=40.769802&passenger_count=2](http://localhost:8000/predict?pickup_datetime=2014-07-06%2019:18:00&pickup_longitude=-73.950655&pickup_latitude=40.783282&dropoff_longitude=-73.984365&dropoff_latitude=40.769802&passenger_count=2)

👉 Or call from your CLI

```bash
curl -X 'GET' \
  'http://localhost:8000/predict?pickup_datetime=2014-07-06+19:18:00&pickup_longitude=-73.950655&pickup_latitude=40.783282&dropoff_longitude=-73.984365&dropoff_latitude=40.769802&passenger_count=2' \
  -H 'accept: application/json'
```

When and **only when** your API responds as required:
1. 🧪 Test your implementation with `make test_api_predict`
2. 🧪 Track your progress on Kitt with  `make test_kitt` & push your code!

**👏 Congrats, you've built your first ML predictive API!**

<br>

### ⚡️ Faster Predictions

Did you notice your predictions were a bit slow? Why do you think that is?

The answer is visible in your logs!

We want to avoid loading the heavy Deep Learning model from MLflow at each `GET` request! The trick is to load the model into memory on startup and store it in a global variable in `app.state`, which is kept in memory and accessible across all routes!

This will prove very useful for Demo Days!

<details>
  <summary markdown='span'>⚡️ like this ⚡️</summary>

```python
app = FastAPI()
app.state.model = ...

@app.get("/predict")
...
app.state.model.predict(...)
```

</details>



</details>


# 3️⃣ Build a Docker Image for our API 🐳

<details>
  <summary markdown='span'><strong>❓ Instructions </strong></summary>

We now have a working **predictive API** that can be queried from our local machine.

We want to make it available to the world. To do that, the first step is to create a **Docker image** that contains the environment required to run the API and make it run _locally_ on Docker.

**❓ What are the 3 steps to run the API on Docker?**

<details>
  <summary markdown='span'>Answer</summary>

1. **Create** a `Dockerfile` containing the instructions to build the API
2. **Build** the image
3. **Run** the API on Docker (locally) to ensure that it is responding as required

</details>

## 3.1) Setup

You need to have the Docker daemon running on your machine to be able to build and run the image.

**💻 Launch Docker Daemon**

<details>
  <summary markdown='span'>macOS</summary>

Launch the Docker app, you should see a whale on your menu bar.

<a href="https://wagon-public-datasets.s3.amazonaws.com/data-science-images/DE/macos-docker-desktop-running.png" target="_blank"><img src="https://wagon-public-datasets.s3.amazonaws.com/data-science-images/DE/macos-docker-desktop-running.png" width="150" alt="verify that Docker Desktop is running"></a>

</details>

<details>
  <summary markdown='span'>Windows WSL2 & Ubuntu</summary>

Launch the Docker app, you should see a whale on your taskbar (Windows).

<a href="https://wagon-public-datasets.s3.amazonaws.com/data-science-images/DE/windows-docker-app.png" target="_blank"><img src="https://wagon-public-datasets.s3.amazonaws.com/data-science-images/DE/windows-docker-app.png" width="150" alt="verify that Docker Desktop is running"></a>

</details>

**✅ Check whether the Docker daemon is up and running with `docker info` in your Terminal**

A nice stack of logs should print:
<br>
<a href="https://github.com/lewagon/data-setup/raw/master/images/docker_info.png" target="_blank"><img src='https://github.com/lewagon/data-setup/raw/master/images/docker_info.png' width=150></a>


## 3.2) `Dockerfile`

As a reminder, here is the project directory structure:

```bash
.
├── Dockerfile          # 🆕 Building instructions
├── Makefile
├── README.md
├── requirements.txt    # All the dependencies you need to run the package
├── setup.py            # Package installer
├── taxifare
│   ├── api
│   │   ├── __init__.py
│   │   └── fast.py     # ✅ Where the API lays
│   ├── interface       # Manual entry points
│   └── ml_logic
└── tests
```

**❓ What are the key ingredients a `Dockerfile` needs to cook a delicious Docker image?**

<details>
  <summary markdown='span'>Answer</summary>

Here are the most common instructions for any good `Dockerfile`:
- `FROM`: select a base image for our image (the environment in which we will run our code), this is usually the first instruction
- `COPY`: copy files and directories into our image (our package and the associated files, for example)
- `RUN`: execute a command **inside** of the image being built (for example, `pip install -r requirements.txt` to install package dependencies)
- `CMD`: the **main** command that will be executed when we run our **Docker image**. There can only be one `CMD` instruction in a `Dockerfile`. It is usually the last instruction!

</details>

**❓ What should the base image contain so we can build our image on top of it?**

<details>
  <summary markdown='span'>💡 Hints</summary>

You can start from a raw Linux (Ubuntu) image, but then you'll have to install Python and `pip` before installing `taxifare`!

OR

You can choose an image with Python (and pip) already installed! (recommended) ✅

</details>

**💻 In the `Dockerfile`, write the instructions needed to build the API image following these specifications:** <br>
_Feel free to use the checkboxes below to help you keep track of what you've already done_ 😉


The image should contain:
<br>
<input type="checkbox" id="dockertask1" name="dockertask1" style="margin-left: 20px;">
<label for="dockertask1"> the same Python version of your virtual env</label><br>
<input type="checkbox" id="dockertask2" name="dockertask2" style="margin-left: 20px;">
<label for="dockertask2"> all the directories from the `/taxifare` project needed to run the API</label><br>
<input type="checkbox" id="dockertask3" name="dockertask3" style="margin-left: 20px;">
<label for="dockertask3"> the list of dependencies (don't forget to install them!)</label><br>

The web server should:
<br>
<input type="checkbox" id="dockertask4" name="dockertask4" style="margin-left: 20px;">
<label for="dockertask4"> launch when a container is started from the image</label><br>
<input type="checkbox" id="dockertask5" name="dockertask5" style="margin-left: 20px;">
<label for="dockertask5"> listen to the HTTP requests coming from outside the container (see `host` parameter)</label><br>
<input type="checkbox" id="dockertask6" name="dockertask6" style="margin-left: 20px;">
<label for="dockertask6"> be able to listen to a specific port defined by an environment variable `$PORT` (see `port` parameter)</label><br>

<details>
  <summary markdown='span'>⚡️ Kickstart pack</summary>

Here is the skeleton of the `Dockerfile`:

  ```Dockerfile
  FROM image
  COPY taxifare
  COPY dependencies
  RUN install dependencies
  CMD launch API web server
  ```

</details>


**❓ How do you check if the `Dockerfile` instructions will execute what you want?**

<details>
  <summary markdown='span'>Answer</summary>

You can't at this point! 😁 You need to build the image and check if it contains everything required to run the API. Go to the next section: Build the API image.
</details>

## 3.3) Build the API image

Now is the time to **build** the API image so you can check if it satisfies all requirements, and to be able to run it on Docker.

**💻 Choose a Docker image name and add it to your `.env`**.
You will be able to reuse it in the `docker` commands:

``` bash
GAR_IMAGE=taxifare
```

**💻 Then, make sure you are in the directory of the `Dockefile` and build `.`** :

```bash
docker build --tag=$GAR_IMAGE:dev .
```


**💻 Once built, the image should be visible in the list of images built with the following command**:

``` bash
docker images
```
<img src='https://wagon-public-datasets.s3.amazonaws.com/data-science-images/07-ML-OPS/docker_images.png'>

🤔 The image you are looking for does not appear in the list? Ask for help 🙋‍♂️

## 3.4) Check the API Image

Now that the image is built, let's verify that it satisfies the specifications to run the predictive API. Docker comes with a handy command to **interactively** communicate with the shell of the image:

``` bash
docker run -it -e PORT=8000 -p 8000:8000 $GAR_IMAGE:dev sh
```

<details>
  <summary markdown='span'>🤖 Command composition</summary>

- `docker run $GAR_IMAGE`: run the image
- `-it`: enable the interactive mode
- `-e PORT=8000`: specify the environment variable `$PORT` to which the image should listen
- `sh`: launch a shell console
</details>

A shell console should open, you are now inside the image 👏

**💻 Verify that the image is correctly set up:**

<input type="checkbox" id="dockertask7" name="dockertask7" style="margin-left: 20px;">
<label for="dockertask7"> The python version is the same as in your virtual env</label><br>
<input type="checkbox" id="dockertask8" name="dockertask8" style="margin-left: 20px;">
<label for="dockertask8"> The `/taxifare` directory exists</label><br>
<input type="checkbox" id="dockertask9" name="dockertask9" style="margin-left: 20px;">
<label for="dockertask9"> The `requirements.txt` file exists</label><br>
<input type="checkbox" id="dockertask10" name="dockertask10" style="margin-left: 20px;">
<label for="dockertask10"> The dependencies are all installed</label><br>

<details>
  <summary markdown='span'>🙈 Solution</summary>

- `python --version` to check the Python version
- `ls` to check the presence of the files and directories
- `pip list` to check if requirements are installed
</details>

Exit the terminal and stop the container at any moment with:

``` bash
exit
```

**✅ ❌ All good? If something is missing, you will probably need to fix your `Dockerfile` and re-build the image**

## 3.5) Run the API Image

In the previous section you learned how to interact with the shell inside the image. Now is the time to run the predictive API image and test if the API responds as it should.

**💻 Try to actually run the image**

You want to `docker run ...` without the `sh` command at the end, so as to trigger the `CMD` line of your Dockerfile, instead of just opening a shell.

``` bash
docker run -it -e PORT=8000 -p 8000:8000 $GAR_IMAGE:dev
```

**😱 It is probably crashing with errors involving environment variables**

**❓ What's wrong? What's the difference between your local environment and your image environment? 💬 Discuss with your buddy.**

<details>
  <summary markdown='span'>Answer</summary>

There is **no** `.env` in the image! The image has **no** access to the environment variables 😈
</details>

**💻 Adapt the run command so the `.env` is sent to the image (use `docker run --help` to help you!)**

<details>
  <summary markdown='span'>🙈 Solution</summary>

`--env-file` to the rescue!

```bash
docker run -e PORT=8000 -p 8000:8000 --env-file your/path/to/.env $GAR_IMAGE:dev
```
</details>

**❓ How would you check that the image runs correctly?**

<details>
  <summary markdown='span'>💡 Hints</summary>

The API should respond in your browser, go visit it!

Also, you can check if the image runs with `docker ps` in a new Terminal tab or window

</details>


### It's alive! 😱 🎉

<br>

**👀 Inspect your browser response 👉 [http://localhost:8000/predict?pickup_datetime=2014-07-06&19:18:00&pickup_longitude=-73.950655&pickup_latitude=40.783282&dropoff_longitude=-73.984365&dropoff_latitude=40.769802&passenger_count=2](http://localhost:8000/predict?pickup_datetime=2014-07-06&19:18:00&pickup_longitude=-73.950655&pickup_latitude=40.783282&dropoff_longitude=-73.984365&dropoff_latitude=40.769802&passenger_count=2)**

**🛑 You can stop your container with `docker container stop <CONTAINER_ID>`**


👏 Congrats, you've built your first ML predictive API inside a Docker container!

<br>


## 3.6) Optimized image

### 3.6.1) Smarter image 🧠

**🤔 How do you avoid rebuilding all pip dependencies each time taxifare code is changed?**

<details>
  <summary markdown='span'>🎁 Solution</summary>

By leveraging Docker caching layer per layer. If you don't update a deeper layer, docker will not rebuild it!

```Dockerfile
FROM python:3.8.12-buster

WORKDIR /prod

# First, pip install dependencies
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

# Then only, install taxifare!
COPY taxifare taxifare
COPY setup.py setup.py
RUN pip install .

# ...

```

</details>


**🤔 How do you make use of the local caching mechanism we put in place for CSVs and Models?**

<details>
  <summary markdown='span'>🎁 Solution</summary>

By recreating the same local storage structure !

```Dockerfile
# [...]
COPY taxifare taxifare
COPY setup.py setup.py
RUN pip install .

# We already have a make command for that!
COPY Makefile Makefile
RUN make reset_local_files

```
</details>

### 3.6.2) Lighter image 🪶

As a responsible ML Engineer, you know that the size of an image is important when it comes to production. Depending on the base image you used in your `Dockerfile`, the API image could be huge:
- `python:3.8.12-buster` 👉 `3.9GB`
- `python:3.8.12-slim`   👉 `3.1GB`
- `python:3.8.12-alpine` 👉 `3.1GB`

**❓ What is the heaviest requirement used by your API?**

<details>
  <summary markdown='span'>Answer</summary>

No doubt it is `tensorflow` with 1.1GB! Let's find a base image that is already optimized for it.
</details>

**📝 Change your base image [If you DO NOT have an Mac Silicon (M-Chip) or ARM CPU]**

<details>
  <summary markdown='span'>Instructions</summary>

Let's use a [tensorflow docker image](https://hub.docker.com/r/tensorflow/tensorflow) instead! It's a Ubuntu with Python and Tensorflow already installed!

- 💻 Update your `Dockerfile` base image with either `tensorflow/tensorflow:2.10.0` (if you are on an Intel processor only)
- 💻 Remove `tensorflow` from your `requirements.txt` because it is now pre-build with the image.
- 💻 Build a lightweight local image of your API (you can use a tag:'light' on this new image to differentiate it from the heavy one built previously: `docker build --tag=$GAR_IMAGE:light .`
- ✅ Make sure the API is still up and running
- 👀 Inspect the space saved with `docker images` and feel happy
</details>


### 3.6.3) Prod-ready image (finally!) ☁️

👏 Everything runs fine on your local machine. Great. We will now deploy your image on servers that are going to run these containers online for you.

However, note that these servers (Google Cloud Run servers) will be running on **AMD/Intel x86 processors**, not ARM/M1, as most cloud providers still run on Intel.

<details>
  <summary markdown='span'><strong>🚨 If you have Mac Silicon (M-chips) or ARM CPU, read carefully</strong></summary>

The solution is to use one image to test your code locally (you have just done it above), and another one to push your code to production.

- Tell Docker to build the image specifically for Intel/AMD processors and give it a new tag:'light-intel':  `docker build --platform linux/amd64 -t $GAR_IMAGE:light-intel .`
- You will **not** be able to run this image locally, but this is the one you will be able push online to the GCP servers!
- You should now have 3 images:
  - `$GAR_IMAGE:dev`
  - `$GAR_IMAGE:light`
  - `$GAR_IMAGE:light-intel`

</details>


**📝 Make a final image tagged "prod", by removing useless python packages**
- Create `requirement_prod.txt` by stripping-out `requirement.txt` from anything you will not need in production (e.g pytest, ipykernel, matplotlib etc...)
- Build your final image and tag it `docker build -t $GAR_IMAGE:light-intel .`


</details>


# 4️⃣ Deploy the API 🌎

<details>
  <summary markdown='span'><strong>❓Instructions </strong></summary>

Now that we have built a **predictive API** Docker image that we can run on our local machine, we are 2 steps away from deploying; we just need to:
1. push the **Docker image** to **Google Artifact Registry**
2. deploy the image on **Google Cloud Run** so that it gets instantiated into a **Docker container**

## 4.1) Push our prod image to Google Artifact Registry

**❓What is the purpose of Google Artifact Registry?**

<details>
  <summary markdown='span'>Answer</summary>

**Google Artifact Registry** is a cloud storage service for Docker (and similar technology) images with the purpose of allowing **Cloud Run** or **Kubernetes Engine** to serve them.

It is, in a way, similar to **GitHub** allowing you to store your git repositories in the cloud — except Google Artifact Registry lacks a dedicated user interface and additional services such as `forks` and `pull requests`).

</details>


### Build and Push the Image to GAR

Now we are going to build our image again. This should be pretty fast since Docker is smart and is going to reuse all the building blocks that were previously used to build the prediction API image.

First, let's make sure to enable the [Google Artifact Registry API](https://console.cloud.google.com/flows/enableapi?apiid=artifactregistry.googleapis.com&redirect=https://cloud.google.com/artifact-registry/docs/docker/store-docker-container-images) for your project in GCP.

Once this is done, let's allow the `docker` command to push an image to GCP within our region.

``` bash
gcloud auth configure-docker $GCP_REGION-docker.pkg.dev
```

Lets create a repo in that region as well!

```bash
gcloud artifacts repositories create taxifare --repository-format=docker \
--location=$GCP_REGION --description="Repository for storing taxifare images"
```

Lets build our image ready to push to that repo

``` bash
docker build -t  $GCP_REGION-docker.pkg.dev/$GCP_PROJECT/taxifare/$GAR_IMAGE:prod .
```

Again, let's make sure that our image runs correctly, so as to avoid wasting time pushing a broken image to the cloud.

``` bash
docker run -e PORT=8000 -p 8000:8000 --env-file .env $GCP_REGION-docker.pkg.dev/$GCP_PROJECT/taxifare/$GAR_IMAGE:prod
```
Visit [http://localhost:8000/](http://localhost:8000/) and check whether the API is running as expected.

We can now push our image to Google Artifact Registry.

``` bash
docker push $GCP_REGION-docker.pkg.dev/$GCP_PROJECT/taxifare/$GAR_IMAGE:prod
```

The image should be visible in the [GCP console](https://console.cloud.google.com/artifacts/).

## 4.2) Deploy the Artifact Registry Image to Google Cloud Run

Add a `--memory` flag to your project configuration and set it to `2Gi` (use `GAR_MEMORY` in `.env`)

👉 This will allow your container to run with **2GiB (= [Gibibyte](https://simple.wikipedia.org/wiki/Gibibyte))** of memory

**❓ How does Cloud Run know the values of the environment variables to be passed to your container? Discuss with your buddy 💬**

<details>
  <summary markdown='span'>Answer</summary>

It does not. You need to provide a list of environment variables to your container when you deploy it 😈

</details>

**💻 Using the `gcloud run deploy --help` documentation, identify a parameter that allows you to pass environment variables to your container on deployment**

<details>
  <summary markdown='span'>Answer</summary>

The `--env-vars-file` is the correct one!

```bash
gcloud run deploy --env-vars-file .env.yaml
```

<br>
Tough luck, the `--env-vars-file` parameter takes as input the name of a YAML (pronounced "yemil") file containing the list of environment variables to be passed to the container.

</details>

**💻 Create a `.env.yaml` file containing all the necessary environment variables**

You can use the provided `.env.sample.yaml` file as a source for the syntax (do not forget to update the values of the parameters). All values should be strings

**❓ What is the purpose of Cloud Run?**

<details>
  <summary markdown='span'>Answer</summary>

Cloud Run will instantiate the image into a container and run the `CMD` instruction inside of the `Dockerfile` of the image. This last step will start the `uvicorn` server, thus serving our **predictive API** to the world 🌍

</details>

Let's run one last command 🤞

``` bash
gcloud run deploy --image $GCP_REGION-docker.pkg.dev/$GCP_PROJECT/taxifare/$GAR_IMAGE:prod --memory $GAR_MEMORY --region $GCP_REGION --env-vars-file .env.yaml
```

After confirmation, you should see something like this, indicating that the service is live 🎉

```bash
Service name (wagon-data-tpl-image):
Allow unauthenticated invocations to [wagon-data-tpl-image] (y/N)?  y

Deploying container to Cloud Run service [wagon-data-tpl-image] in project [le-wagon-data] region [europe-west1]
✓ Deploying new service... Done.
  ✓ Creating Revision... Revision deployment finished. Waiting for health check to begin.
  ✓ Routing traffic...
  ✓ Setting IAM Policy...
Done.
Service [wagon-data-tpl-image] revision [wagon-data-tpl-image-00001-kup] has been deployed and is serving 100 percent of traffic.
Service URL: https://wagon-data-tpl-image-xi54eseqrq-ew.a.run.app
```

🧪 Write down your service URL in your local `.env` file so we can test it!

```bash
SERVICE_URL=https://wagon-data-tpl-image-xi54eseqrq-ew.a.run.app
```

Then finally,

```bash
direnv reload
make test_api_on_prod
make test_kitt
```

**👏👏👏👏 MASSIVE CONGRATS 👏👏👏**
You deployed your first ML predictive API!
Any developer in the world 🌍 is now able to browse to the deployed url and get a prediction using the API 🤖!

<br>

## 4.3) Stop everything and save money 💸

⚠️ Keep in mind that you pay for the service as long as it is up 💸

You can look for any running cloud run services using

``` bash
gcloud run services list
```

You can shut down any instance with

``` bash
gcloud run services delete $INSTANCE
```

You can also stop (or kill) your local docker image to free up memory on your local machine

``` bash
docker stop 152e5b79177b  # ⚠️ use the correct CONTAINER ID
docker kill 152e5b79177b  # ☢️ only if the image refuses to stop (did someone create an ∞ loop?)
```
Remember to stop the Docker daemon in order to free resources on your machine once you are done using it.

<details>
  <summary markdown='span'>macOS</summary>

Stop the `Docker.app` by clicking on **whale > Quit Docker Desktop** in the menu bar.
</details>

<details>
  <summary markdown='span'>Windows WSL2/Ubuntu</summary>

Stop the Docker app by right-clicking the whale on your taskbar.
</details>

</details>


# 5️⃣ OPTIONAL

<details>
  <summary markdown='span'><strong>❓ Instructions </strong></summary>

## 1) Create a /POST request to be able to return batch predictions

Let's look at our `/GET` route format

```bash
http://localhost:8000/predict?pickup_datetime=2014-07-06&19:18:00&pickup_longitude=-73.950655&pickup_latitude=40.783282&dropoff_longitude=-73.984365&dropoff_latitude=40.769802&passenger_count=2
```

🤯 How would you send a prediction request for 1000 rows at once?

The URL query string (everything after `?` in the URL above) is not able to send a large volume of data.

### Welcome to `/POST` HTTP Requests

- Your goal is to be able to send a batch of 1000 new predictions at once!
- Try to read more about POST in the [FastAPI docs](https://fastapi.tiangolo.com/tutorial/body/#request-body-path-query-parameters), and implement it in your package

## 2) Read about sending images 📸 via /POST requests to CNN models

In anticipation of your Demo Day, you might be wondering how to send unstructured data like images (or videos, sounds, etc.) to your Deep Learning model in prod.


👉 Bookmark [Le Wagon - data-template](https://github.com/lewagon/data-templates), and try to understand & reproduce the project boilerplate called "[sending-images-streamlit-fastapi](https://github.com/lewagon/data-templates/tree/main/project-boilerplates/sending-images-streamlit-fastapi)"


</details>
