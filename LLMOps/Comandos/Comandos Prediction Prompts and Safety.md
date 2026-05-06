# L4: Predictions, Prompts and Safety

- Load the Project ID and credentials.


```python
from utils import authenticate
credentials, PROJECT_ID = authenticate() 
```


```python
REGION = "us-central1"
```

- Import the [Vertex AI](https://cloud.google.com/vertex-ai) SDK.
- Import and load the model.
- Initialize it.


```python
import vertexai
from vertexai.language_models import TextGenerationModel
```


```python
vertexai.init(project = PROJECT_ID,
              location = REGION,
              credentials = credentials)
```

## Deployment

### Load Balancing

- Load from pre-trained `text-bison@001`
- Retrieve the endpoints (deployed as REST API)


```python
model = TextGenerationModel.from_pretrained("text-bison@001")
```

- Get the list of multiple models executed and deployed.
- This helps to rout the traffic to different endpoints.


```python
list_tuned_models = model.list_tuned_model_names()
```


```python
for i in list_tuned_models:
    print (i)
```

- Randomly select from one of the endpoints to divide the prediction load.


```python
import random
```


```python
tuned_model_select = random.choice(list_tuned_models)
```

### Getting the Response

- Load the endpoint of the randomly selected model to be called with a prompt.
- The prompt needs to be as similar as possible as the one you trained your model on (python questions from stack overflow dataset)


```python
deployed_model = TextGenerationModel.get_tuned_model\
(tuned_model_select)
```


```python
PROMPT = "How can I load a csv file using Pandas?"
```

- Use `deployed_model.predit` to call the API using the prompt. 


```python
### depending on the latency of your prompt
### it can take some time to load
response = deployed_model.predict(PROMPT)
```


```python
print(response)
```

- `pprint` makes the response easily readable.


```python
from pprint import pprint
```

- Sending multiple prompts can return multiple responses `([0], [1], [2]...)`
- In this example, only 1 prompt is being sent, and returning only 1 response `([0])`


```python
### load the first object of the response
output = response._prediction_response[0]
```


```python
### print the first object of the response
pprint(output)
```


```python
### load the second object of the response
output = response._prediction_response[0][0]
```


```python
### print the second object of the response
pprint(output)
```


```python
### retrieve the "content" key from the second object
final_output = response._prediction_response[0][0]["content"]
```


```python
### printing "content" key from the second object
print(final_output)
```

#### Prompt Management and Templates
- Remember that the model was trained on data that had an `Instruction` and a `Question` as a `Prompt` (Lesson 2).
- In the example above, *only*  a `Question` as a `Prompt` was used for a response.
- It is important for the production data to be the same as the training data. Difference in data can effect the model performance.
- Add the same `Instruction` as it was used for training data, and combine it with a `Question` to be used as a `Prompt`.


```python
INSTRUCTION = """\
Please answer the following Stackoverflow question on Python.\
Answer it like\
you are a developer answering Stackoverflow questions.\
Question:
"""
```


```python
QUESTION = "How can I store my TensorFlow checkpoint on\
Google Cloud Storage? Python example?"
```

- Combine the intruction and the question to create the prompt.


```python
PROMPT = f"""
{INSTRUCTION} {QUESTION}
"""
```


```python
print(PROMPT)
```

- Get the response using the new prompt, which is consistent with the prompt used for training.


```python
final_response = deployed_model.predict(PROMPT)
```


```python
output = final_response._prediction_response[0][0]["content"]
```

- Note how the response changed from earlier.


```python
print(output)
```

### Safety Attributes
- The reponse also includes safety scores.
- These scores can be used to make sure that the LLM's response is within the boundries of the expected behaviour.
- The first layer for this check, `blocked`, is by the model itself.


```python
### retrieve the "blocked" key from the 
### "safetyAttributes" of the response
blocked = response._prediction_response[0][0]\
['safetyAttributes']['blocked']
```

- Check to see if any response was blocked.
- It returns `True` if there is, and `False` if there's none.


```python
print(blocked)
```

- The second layer of this check can be defined by you, as a practitioner, according to the thresholds you set.
- The response returns probabilities for each safety score category which can be used to design the thresholds.


```python
### retrieve the "safetyAttributes" of the response
safety_attributes = response._prediction_response[0][0]\
['safetyAttributes']
```


```python
pprint(safety_attributes)
```

### Citations
- Ideally, a LLM should generate as much original cotent as possible.
- The `citationMetadata` can be used to check and reduce the chances of a LLM generating a lot of existing content.


```python
### retrieve the "citations" key from the 
### "citationMetadata" of the response
citation = response._prediction_response[0][0]\
['citationMetadata']['citations']
```

- Returns a list with information if the response is cited, if not, then it retuns an empty list.


```python
pprint(citation)
```

## Try it Yourself! - Return a Citation

Now it is time for you to come up with an example, for which the model response should return citation infomration. The idea here is to see how that would look like, so keeping it basic, the prompt can be different than the coding examples used above. Below code is one such prompt:


```python
PROMPT = "Finish the sentence: To be, or not "
```


```python
response = deployed_model.predict(PROMPT)
```


```python
### output of the model
output = response._prediction_response[0][0]["content"]
print(output)
```


```python
### check for citation
citation = response._prediction_response[0][0]\
['citationMetadata']['citations']

pprint(citation)
```

**Your turn!**


```python

```


```python

```


```python

```


```python

```

### Optional Notebook

[Tuning and deploy a foundation model](https://github.com/GoogleCloudPlatform/generative-ai/blob/main/language/tuning/tuning_text_bison.ipynb)


```python

```
