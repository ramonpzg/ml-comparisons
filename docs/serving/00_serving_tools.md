# Overview of MLServer and Ray Serve


The following comparison between MLServer and Ray Serve covers different use cases 
and scenarios to highlight the strengths and drawbacks of each. You can follow along 
in a notebook locally, on Google Colab, or via a GitHub Codespace using the boxes 
above.

Let's get started!

## Installation

=== "MLServer"

    ```sh
    pip install mlserver mlserver-huggingface
    ```

=== "Ray Serve"

    ```sh
    pip install "ray[serve]" transformers
    ```

## Import Packages

=== "MLServer"

    ```python
    import mlserver
    ```

=== "Ray Serve"

    ```python
    import serve
    ```


## Serving a Model

With Ray you can create an app as a regular Python file and define a class 
that will load your model and run inference with it. The class will get a `serve` decorator 
to indicate ray what object in your Python file will become a service. 

```python
# File name: serve_deployment.py
from starlette.requests import Request
from transformers import pipeline
from ray import serve
import ray


@serve.deployment(num_replicas=2, ray_actor_options={"num_cpus": 0.2, "num_gpus": 0})
class Translator:
    def __init__(self):
        # Load model
        self.model = pipeline("translation_en_to_fr", model="t5-small")

    def translate(self, text: str) -> str:
        # Run inference
        model_output = self.model(text)

        # Post-process output to return only the translation text
        translation = model_output[0]["translation_text"]

        return translation

    async def __call__(self, http_request: Request) -> str:
        english_text: str = await http_request.json()
        return self.translate(english_text)


translator_app = Translator.bind()
``