To deploy a cloud function to Google Cloud Platform you run: 
```bash
gcloud functions deploy processSpeedtestEvent --trigger-resource speedtest --trigger-event google.pubsub.topic.publish --runtime nodejs8 --region europe-west1
```
Where
* `processSpeedtestEvent` is the name the function will have in your GCP project, and when `--entry-point` is not specified it must also be the name of a function exported in `index.js`
* `--trigger-resource`, in the case of Pub/Sub triggered functions, is the topic that it shall subscribe to
* `--trigger-event google.pubsub.topic.publish` states that the function shall be triggered by Pub/Sub messages

Declared dependencies in `package.json` will be installed automatically when you deploy the function.