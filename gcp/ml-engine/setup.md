# Setup

## Clone repository

Lets start by `cloning` a repository containing a model which uses `Keras` to train on the United States Census data.

```bash
git clone REPO
```

## Install dependencies

We are here using `pipenv` to install our `python` dependencies.

### Installing `pipenv`

#### Mac and linux
See https://github.com/pypa/pipenv#installation

#### Windows
??

#### Using virtual env

```bash
pip install --user --upgrade virtualenv
```

```bash
virtualenv cmle-env
source cmle-env/bin/activate
```

### Installing dependencies with `pipenv`

When we have `pipenv` installed we can activate the `pipenv` environment and install the dependencies:
```bash
pipenv shell
pipenv install --dev
```

### Installing dependencies with `pip`

An alternative to using `pipenv` is using `pip` to install our dependencies
```bash
pip install -r requirements.txt
```

## Create bucket

Lets create a bucket in our gcp project where we will store our job(s)

The bucket name has to be unique across google cloud storage, but so does our gcp-project-id need to be, so lets use our project-id and some extra name, to name our bucket.
```bash
PROJECT_ID=$(gcloud config list project --format "value(core.project)")
BUCKET_NAME=${PROJECT_ID}-mlengine
```

Then we can set a region, which we want our bucket to live in. This one can not be regional.
See here for options: https://cloud.google.com/ml-engine/docs/tensorflow/regions

`europe-west1` seems to have the options we would want, and is relatively close.
```bash
REGION=europe-west1
```

With this set up, we can create our bucket.
```bash
gsutil mb -l $REGION gs://$BUCKET_NAME
```
