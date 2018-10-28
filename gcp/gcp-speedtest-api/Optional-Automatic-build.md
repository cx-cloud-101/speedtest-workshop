You can setup builds with GCP Cloud Build to automatically trigger when you push new commits to a branch. 
Read about how to set up your GitHub repository with cloud build: [Installing the Google Cloud Build app](https://cloud.google.com/cloud-build/docs/run-builds-on-github#installing_the_google_cloud_build_app).
Then add a `cloudbuild.yaml` to the root of your repository. The example below runs `gradle test`, but you could easily extend the build to deploy the application to App Engine by using the `gcr.io/cloud-builders/gcloud` container. 
```yaml
steps:
  - name: gcr.io/cloud-builders/gradle
    args:
      - test
```