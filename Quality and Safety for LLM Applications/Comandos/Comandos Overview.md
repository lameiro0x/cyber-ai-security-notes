# Lesson 1: Overview

In this lesson, you will:
1. Explore the dataset of LLM prompts and responses named **chats.csv** that we’ll use throughout this course.
2. Get a fast demo overview of all the techniques showcased in greater detail in later lessons.


## Dataset


```python
import helpers
```


```python
import pandas as pd
```


```python
chats = pd.read_csv("./chats.csv")
```


```python
chats.head(5)
```


```python
pd.set_option('display.max_colwidth', None)
```


```python
chats.head(5)
```

## Setup and explore whylogs and langkit


```python
import whylogs as why
```


```python
why.init("whylabs_anonymous")
```


```python
from langkit import llm_metrics
```


```python
schema = llm_metrics.init()
```


```python
result = why.log(chats,
                 name="LLM chats dataset",
                 schema=schema)
```

### Prompt-response relevance


```python
from langkit import input_output
```

**Note**: To view the next visual, you may have to either hide the left-side menu bar or widen the notebook towards the right.


```python
helpers.visualize_langkit_metric(
    chats,
    "response.relevance_to_prompt"
)
```


```python
helpers.show_langkit_critical_queries(
    chats,
    "response.relevance_to_prompt"
)
```

### Data Leakage


```python
from langkit import regexes
```

**Note**: To view the next visuals, you may have to either hide the left-side menu bar or widen the notebook towards the right.


```python
helpers.visualize_langkit_metric(
    chats,
    "prompt.has_patterns"
)
```


```python
helpers.visualize_langkit_metric(
    chats, 
    "response.has_patterns")
```

### Toxicity


```python
from langkit import toxicity
```

**Note**: To view the next visuals, you may have to either hide the left-side menu bar or widen the notebook towards the right.


```python
helpers.visualize_langkit_metric(
    chats, 
    "prompt.toxicity")
```


```python
helpers.visualize_langkit_metric(
    chats, 
    "response.toxicity")
```

### Injections


```python
from langkit import injections
```

**Note**: To view the next visual, you may have to either hide the left-side menu bar or widen the notebook towards the right.


```python
helpers.visualize_langkit_metric(
    chats,
    "injection"
)
```


```python
helpers.show_langkit_critical_queries(
    chats,
    "injection"
)
```

## Evaluation


```python
helpers.evaluate_examples()
```


```python
filtered_chats = chats[
    chats["response"].str.contains("Sorry")
]
```


```python
filtered_chats
```


```python
helpers.evaluate_examples(filtered_chats)
```


```python
filtered_chats = chats[
    chats["prompt"].str.len() > 250
]
```


```python
filtered_chats
```


```python
helpers.evaluate_examples(filtered_chats)
```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```
