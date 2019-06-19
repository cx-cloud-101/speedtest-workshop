# Training and creating a model

## Train model locally

Lets train a model locally

We specify a variable which will be the output directory of our local run.
```bash
MODEL_DIR=output
```

We run our training locally, like so:
```bash
gcloud ai-platform local train \
  --package-path trainer \
  --module-name trainer.task \
  --job-dir $MODEL_DIR
```

With `tensorboard` we can have a look at the progression of our training. We launch `tensorboard`.
```bash
tensorboard --logdir=$MODEL_DIR --port=8080
```

## Train model using AI platform

Lets train a model on the AI platform in gcp.

First, lets name our job, and set the directory we want it in the cloud.
```bash
JOB_NAME="my_keras_job"
JOB_DIR="gs://$BUCKET_NAME/keras-job-dir"
```

Then we submit our job to the cloud.
```bash
gcloud ai-platform jobs submit training $JOB_NAME \
  --package-path trainer/ \
  --module-name trainer.task \
  --region $REGION \
  --python-version 3.5 \
  --runtime-version 1.13 \
  --job-dir $JOB_DIR \
  --stream-logs
```

## Create model in AI platform

We now create a model, and a version for this model, in the AI platform.

Create the model
```bash
MODEL_NAME="my_keras_model"

gcloud ai-platform models create $MODEL_NAME \
  --regions $REGION
```

Create a model version
```bash
MODEL_VERSION="v1"
```

Get path to latest trained model in cloud
```bash
SAVED_MODEL_PATH=$(gsutil ls $JOB_DIR/keras_export | tail -n 1)
```

Create model version in cloud, could take a couple of minutes
```bash
gcloud ai-platform versions create $MODEL_VERSION \
  --model $MODEL_NAME \
  --runtime-version 1.13 \
  --python-version 3.5 \
  --framework tensorflow \
  --origin $SAVED_MODEL_PATH
```
