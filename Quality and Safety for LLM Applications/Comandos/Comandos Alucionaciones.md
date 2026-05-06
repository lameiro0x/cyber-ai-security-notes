# Lesson 2: Hallucinations




```python
import helpers
```


```python
import evaluate
```


```python
import pandas as pd
```


```python
pd.set_option('display.max_colwidth', None)
```


```python
chats = pd.read_csv("./chats.csv")
```

## Prompt-response relevance

### 1. BLEU score


```python
bleu = evaluate.load("bleu")
```


```python
chats[5:6]
```


```python
bleu.compute(predictions=[chats.loc[2, "response"]], 
             references=[chats.loc[2, "prompt"]], 
             max_order=2)
```


```python
from whylogs.experimental.core.udf_schema import register_dataset_udf
```


```python
@register_dataset_udf(["prompt", "response"], 
                      "response.bleu_score_to_prompt")


def bleu_score(text):
  scores = []
  for x, y in zip(text["prompt"], text["response"]):
    scores.append(
      bleu.compute(
        predictions=[x], 
        references=[y], 
        max_order=2
      )["bleu"]
    )
  return scores
```

**Note**: To view the next visual, you may have to either hide the left-side menu bar or widen the notebook towards the right.


```python
helpers.visualize_langkit_metric(
    chats, 
    "response.bleu_score_to_prompt", 
    numeric=True)
```


```python
helpers.show_langkit_critical_queries(
    chats, 
    "response.bleu_score_to_prompt", 
    ascending=True)
```

## 2. BERT score


```python
bertscore = evaluate.load("bertscore")
```


```python
bertscore.compute(
    predictions=[chats.loc[2, "prompt"]],
    references=[chats.loc[2, "response"]],
    model_type="distilbert-base-uncased")
```


```python
@register_dataset_udf(["prompt", "response"], "response.bert_score_to_prompt")
def bert_score(text):
  return bertscore.compute(
      predictions=text["prompt"].to_numpy(),
      references=text["response"].to_numpy(),
      model_type="distilbert-base-uncased"
    )["f1"]
```

**Note**: To view the next visual, you may have to either hide the left-side menu bar or widen the notebook towards the right.


```python
helpers.visualize_langkit_metric(
    chats, 
    "response.bert_score_to_prompt", 
    numeric=True)
```


```python
helpers.show_langkit_critical_queries(
    chats, 
    "response.bert_score_to_prompt", 
    ascending=True)
```


```python
from whylogs.experimental.core.udf_schema import udf_schema
```


```python
annotated_chats, _ = udf_schema().apply_udfs(chats)
```

**Note**: To view the next visuals, you may have to either hide the left-side menu bar or widen the notebook towards the right.


```python
helpers.evaluate_examples(
  annotated_chats[annotated_chats["response.bert_score_to_prompt"] <= 0.75],
  scope="hallucination")
```


```python
helpers.evaluate_examples(
  annotated_chats[annotated_chats["response.bert_score_to_prompt"] <= 0.6],
  scope="hallucination")
```

## Response self-similarity


```python
chats_extended = pd.read_csv("./chats_extended.csv")
```


```python
chats_extended.head(5)
```

## 1. Sentence embedding cosine distance


```python
from sentence_transformers import SentenceTransformer
```


```python
model = SentenceTransformer('all-MiniLM-L6-v2')
```


```python
model.encode("This is a sentence to encode.")
```


```python
from sentence_transformers.util import pairwise_cos_sim
```


```python
@register_dataset_udf(["response", "response2", "response3"], 
                      "response.sentence_embedding_selfsimilarity")
def sentence_embedding_selfsimilarity(text):
  response_embeddings = model.encode(text["response"].to_numpy())
  response2_embeddings = model.encode(text["response2"].to_numpy())
  response3_embeddings = model.encode(text["response3"].to_numpy())
  
  cos_sim_with_response2 = pairwise_cos_sim(
    response_embeddings, response2_embeddings
    )
  cos_sim_with_response3  = pairwise_cos_sim(
    response_embeddings, response3_embeddings
    )
  
  return (cos_sim_with_response2 + cos_sim_with_response3) / 2
```


```python
sentence_embedding_selfsimilarity(chats_extended)
```

**Note**: To view the next visual, you may have to either hide the left-side menu bar or widen the notebook towards the right.


```python
helpers.visualize_langkit_metric(
    chats_extended, 
    "response.sentence_embedding_selfsimilarity", 
    numeric=True)
```


```python
helpers.show_langkit_critical_queries(
    chats_extended, 
    "response.sentence_embedding_selfsimilarity", 
    ascending=True)
```


```python
annotated_chats, _ = udf_schema().apply_udfs(chats_extended)
```


```python
annotated_chats.head(5)
```

## 2. LLM self-evaluation


```python
import openai
```


```python
import helpers
```


```python
openai.api_key = helpers.get_openai_key()
openai.base_url = helpers.get_openai_base_url()
```


```python
def prompt_single_llm_selfsimilarity(dataset, index):
    return openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=[{
            "role": "system",
            "content": f"""You will be provided with a text passage \
            and your task is to rate the consistency of that text to \
            that of the provided context. Your answer must be only \
            a number between 0.0 and 1.0 rounded to the nearest two \
            decimal places where 0.0 represents no consistency and \
            1.0 represents perfect consistency and similarity. \n\n \
            Text passage: {dataset['response'][index]}. \n\n \
            Context: {dataset['response2'][index]} \n\n \
            {dataset['response3'][index]}."""
        }]
    )
```


```python
prompt_single_llm_selfsimilarity(chats_extended, 0)
```


```python
chats_extended[
chats_extended["response.prompted_selfsimilarity"] <= 0.8
]
```
