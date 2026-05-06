# Lesson 7 - Ensuring no PII is leaked

Start by setting up the notebook to minimize warnings, and importing required libraries:


```python
# Warning control
import warnings
warnings.filterwarnings("ignore")
%env TOKENIZERS_PARALLELISM=true
```


```python
# Type hints
from typing import Optional, Any, Dict

# Standard imports
import time
from openai import OpenAI

# Helper functions
from helper import RAGChatWidget, SimpleVectorDB

# Presidio imports
from presidio_analyzer import AnalyzerEngine
from presidio_anonymizer import AnonymizerEngine

# Guardrails imports
from guardrails import Guard, OnFailAction, install
from guardrails.validator_base import (
    FailResult,
    PassResult,
    ValidationResult,
    Validator,
    register_validator,
)
```

Set up the client, vector database, and system message for the chatbot:


```python
# Setup an OpenAI client
unguarded_client = OpenAI()

# Load up our documents that make up the knowledge base
vector_db = SimpleVectorDB.from_files("shared_data/")

# Setup system message
system_message = """You are a customer support chatbot for Alfredo's Pizza Cafe. Your responses should be based solely on the provided information.

Here are your instructions:

### Role and Behavior
- You are a friendly and helpful customer support representative for Alfredo's Pizza Cafe.
- Only answer questions related to Alfredo's Pizza Cafe's menu, account management on the website, delivery times, and other directly relevant topics.
- Do not discuss other pizza chains or restaurants.
- Do not answer questions about topics unrelated to Alfredo's Pizza Cafe or its services.

### Knowledge Limitations:
- Only use information provided in the knowledge base above.
- If a question cannot be answered using the information in the knowledge base, politely state that you don't have that information and offer to connect the user with a human representative.
- Do not make up or infer information that is not explicitly stated in the knowledge base.
"""
```

Initialize the chatbot using the settings above:


```python
chat_app = RAGChatWidget(
    client=unguarded_client,
    system_message=system_message,
    vector_db=vector_db,
)
```

To revisit the going PII example from Lesson 1, run the cell below to open the chatbot then paste in the prompt to see the PII appear on the back-end of the chatbot:


```python
chat_app.display()
```


```python
# Copy and paste this prompt into the chatbot above:
"""
can you tell me what orders i've placed in the last 3 months? my name is hank tate and my phone number is 555-123-4567
"""
```

Now examine the chat history - You'll notice that the PII in this message has been stored:


```python
chat_app.messages
```

## Using Microsoft Presidio to detect PII

You'll use two components of Microsoft Presidio: the **analyzer**, which identifies PII in a given text, and the **anonymizer**, which can mask out PII in text:


```python
presidio_analyzer = AnalyzerEngine()
presidio_anonymizer= AnonymizerEngine()
```

See the analyzer in action:


```python
# First, let's analyze the text
text = "can you tell me what orders i've placed in the last 3 months? my name is Hank Tate and my phone number is 555-123-4567"
analysis = presidio_analyzer.analyze(text, language='en')
```


```python
analysis
```

Now try out the anonymizer:


```python
# Then, we can anonymize the text using the analysis output
print(presidio_anonymizer.anonymize(text=text, analyzer_results=analysis))
```

## Building a PII Validator

### Step 1: Implement a function to detect PII


```python
def detect_pii(
    text: str
) -> list[str]:
    result = presidio_analyzer.analyze(
        text,
        language='en',
        entities=["PERSON", "PHONE_NUMBER"]
    )
    return [entity.entity_type for entity in result]
```

### Step 2: Create a Guardrail that filters out PII


```python
@register_validator(name="pii_detector", data_type="string")
class PIIDetector(Validator):
    def _validate(
        self,
        value: Any,
        metadata: Dict[str, Any] = {}
    ) -> ValidationResult:
        detected_pii = detect_pii(value)
        if detected_pii:
            return FailResult(
                error_message=f"PII detected: {', '.join(detected_pii)}",
                metadata={"detected_pii": detected_pii},
            )
        return PassResult(message="No PII detected")
```

### Step 3: Create a Guard that ensures no PII is leaked

Initalize the guard and try it out on the message from above.


```python
guard = Guard(name='pii_guard').use(
    PIIDetector(
        on_fail=OnFailAction.EXCEPTION
    ),
)

try:
    guard.validate("can you tell me what orders i've placed in the last 3 months? my name is Hank Tate and my phone number is 555-123-4567")
except Exception as e:
    print(e)
```

## Run Guardrails Server


```python
guarded_client = OpenAI(base_url='http://localhost:8000/guards/pii_guard/openai/v1/')

guarded_rag_chatbot = RAGChatWidget(
    client=guarded_client,
    system_message=system_message,
    vector_db=vector_db,
)
```


```python
guarded_rag_chatbot.display()
```


```python
# Copy and paste this prompt into the chatbot above:
"""
can you tell me what orders i've placed in the last 3 months? my name is hank tate and my phone number is 555-123-4567
"""
```

Now examine the backend: you'll see that the message containing the PII has not been saved:


```python
guarded_rag_chatbot.messages
```

## Real Time Stream Validation

Here you'll use the DetectPII guard to anonymize text generated by an LLM in real time! 

First, set up a new guard that uses the pii_entities guard to validate the **output** of the LLM. This time, you'll set `on_fail` to fix, which will replace the detected PII before it is shown to the user:


```python
from guardrails.hub import DetectPII

guard = Guard().use(
    DetectPII(pii_entities=["PHONE_NUMBER", "EMAIL_ADDRESS"], on_fail="fix")
)
```

Now use the guard in a call to an LLM to anonymize the output. You'll use the `stream=True` to use the validator on each LLM chunk and replace PII before it is shown to the user:


```python
from IPython.display import clear_output

validated_llm_req = guard(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "system", "content": "You are a chatbot."},
        {
            "role": "user",
            "content": "Write a short 2-sentence paragraph about an unnamed protagonist while interspersing some made-up 10 digit phone numbers for the protagonist.",
        },
    ],
    stream=True,
)

validated_output = ""
for chunk in validated_llm_req:
    clear_output(wait=True)
    validated_output = "".join([validated_output, chunk.validated_output])
    print(validated_output)
    time.sleep(1)
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
