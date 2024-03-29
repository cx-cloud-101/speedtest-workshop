[Home](../) > [GCP](index) > Automate the API Build!
==========================================================
_It's nice not to have to manually build and deploy our API every time we make a change. Let's automate it!_

Suggested solution
------------------
You can setup builds with GCP Cloud Build to automatically trigger when you push new commits to a branch. 

Read about how to set up your GitHub repository with cloud build: [Installing the Google Cloud Build app](https://cloud.google.com/cloud-build/docs/run-builds-on-github#installing_the_google_cloud_build_app).
Then add a `cloudbuild.yaml` to the root of your repository. The example below runs `mvn test`, but you could easily extend the build to deploy the application to App Engine by using the `gcr.io/cloud-builders/gcloud` container. 

```yaml
 steps:
   - name: maven:3-jdk-8
     entrypoint: mvn
     args: ['test']
```
