# Callbacks


## Create custom callback
```python
from langchain_core.callbacks import BaseCallbackHandler

class TestCallback(BaseCallbackHandler):

    def on_llm_start(self, serialized, prompts, **kwargs):
        print(f"Prompts: {prompts}\n\n")

    def on_llm_end(self, response, **kwargs):
        print(f"Response: {response}\n\n")
```


## Evaluate metrics and get prompt/response with callback

```python
from langchain_openai import AzureOpenAI, AzureOpenAIEmbeddings

from ragas import evaluate, RunConfig
from ragas.metrics import faithfulness

score = evaluate(
    dataset=dataset,
    metrics=[faithfulness],
    llm=AzureOpenAI(
        deployment_name="gpt-35-turbo"
    ),
    embeddings=AzureOpenAIEmbeddings(
        azure_deployment="text-embedding-ada-002"
    ),
    raise_exceptions=True,
    callbacks=[TestCallback()],
    is_async=True,
    run_config=RunConfig(
        timeout=10,
        max_retries=10,
        max_wait=60,
        max_workers=1
    )
)

score.to_pandas()
```
