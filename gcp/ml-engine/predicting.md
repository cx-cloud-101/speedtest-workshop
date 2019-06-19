# Predicting

## Prepare prediction input

We will now create a `prediction_input.json` which will be sent to our model in the cloud. We have some python scripts to help us with that, so let's dive into the `python` interpreter.

Like so:
```bash
python
```

We start by importing the `util` package from the `trainer` module.
```python
from trainer import util
```

We then load some data.
```python
_, _, eval_x, eval_y = util.load_data()
```

We then select `20` random rows from the loaded input data, like so:
```python
prediction_input = eval_x.sample(20)
```

And select the actual values for these rows, like so:
```python
prediction_targets = eval_y[prediction_input.index]
```

We can then have a look at the selected input data:
(As these are randomly selected, they will differ from selection to selection)
```python
prediction_input
```

As we can see from this, the number fields have been scaled to a [`z-score`](https://www.statisticshowto.datasciencecentral.com/probability-and-statistics/z-score/), and string values have been converted to int values. Some columns have also been dropped.
For more information on cleaning data see [cleaning data](https://developers.google.com/machine-learning/crash-course/representation/cleaning-data).

We can also have a look at the `prediction_targets`, which is the actual values each of these rows have. Like so:
```python
prediction_targets
```

To create the actual `prediction_input.json` which we want to predict we will create a json-file with newline-delimiters.

We start by importing the `json` package, and dump each row as a json-object:
```python
import json

with open('prediction_input.json', 'w') as json_file:
  for row in prediction_input.values.tolist():
    json.dump(row, json_file)
    json_file.write('\n')
```

We should now have `prediction_input.json`-file in our root of this package. This one will be sent to our model in the cloud, and the model will make prediction on these, and classify these as above 50K or below. So we can exit our python interpreter.
```python
exit()
```

## Submitting the prediction input

```bash
gcloud ai-platform predict \
  --model $MODEL_NAME \
  --version $MODEL_VERSION \
  --json-instances prediction_input.json
```

The response we get here is what the model's prediction for the `prediction_input.json`. Where 0 to 0.5 is a negative value (below 50k) and 0.5 to 1 is a positive value (above 50k).

How does this compare to the expected `prediction_values`?
