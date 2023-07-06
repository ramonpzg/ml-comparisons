# Serving Tools Comparison


A serving tool is one that allows you to wrap a model into a service that will 
be run a process locally or remotely in a cloud setting. Some serving tools like 
mlserver, cog, bentoml, and truss give you functionalities to package your serving 
code into a container with all of its dependencies so that it can be used in any platform.

Another contender in the serving space is Ray and its `serve` module. Serve works a bit differently 
then the packages mentioned above and, in particular, it differs from mlserver in that it is 
a framework rather than a plain python package. Let's unpack that a bit.

TL;DR
MLServer can be used alongside ray while providing additional functionalities not available in 
ray's serve module. Ray is more friendly to data practitioners in that it assumes users have a model and 
an endpoint to send jobs to and get results from. MLServer is more agnostic and makes less 
assumptions about the user's skillset. Ray serve has better documentation and more use cases 
have been tried with.

## Differences
- Ray is a framework and mlserver is a Python package. This means that the former has specific 
ways of achieving an outcome within a set of constrains so that, for example, your model serving 
logic integrates nicely with the other tools available in the framework's ecosystem. The latter has a recipe 
for doing one thing well, packaging and serving models, in a specific yet up-to-the-user way.
- When using Ray, you have a cluster up and running and you can send an application with your model artifact 
logic inside a Python file and a cli command. With mlserver, you can package the model with all of its 
dependencies in a docker container and send it to any kind of cluster, a serverless function, or elsewhere. This 
is a subtle and yet important difference. A container or dockerfile can be sent everywhere an anywhere 
by developers or data practitioners that might not know ray or have a cluster available.
- MLServer uses a standard API definition, the V2 Inference Protocol, while ray serve has no 
particular API standard. This neither a good or a bad thing. Having standards helps keep things 
organized. Think of this as driving on the left versus the right, with no standards, everyone would 
drive on whichever side they feel most comfortable with, but with standards, everyone would feel more 
safe knowing that they can expect drivers to be on one side only.
- Once connected to a cluster, ray allows you to do fractional scaling, which means you can take 30% of a CPU 
or GPU for a model and use the rest for some other piece of your application. MLServer does not orchestrate the 
resources of your clusters but its main parent, Seldon Core, does allow you to do this in a straightforward way.
- Ray server allows you to build an inference graph by making the output of one model be the input of another. 
You can do this with mlserver and also with Seldon Core or KServe once either have access to your mlserver container.
- It is not clear from the documentation that ray serve supports grpc while mlserver supports this out of the box.
- MLServer lives individually in an instance, hence it needs to be manually scaled horizontally/vertically or via a configuration 
file in your deployment platform of choice. Ray serve let's you define how many instances you want per individual 
model file. This means that ray needs the cluster to be set up in order to send these copies to and from the same 
place, say, your laptop, while mlserver relies on other tools to be scaled vertically or horizontally. But, this also 
means ray can in theory send copies of mlserver to different instances.
- Once connected to a cluster, ray can find and orchestrate the resources needed or required to serve a model. If mlserver 
doesn't have enough resources to meet the requirements it needs to serve a model, the file will fail.
- MLServer is natively integrated fastapi and pydantic while ray serve gives you different serving options such as starlette, 
fastapi, gradio, and your custom server.
- You can integrate Gradio UI's within you ray serve application but you cannot create multiple replicas of it (according to 
the documentation). It is not clear whether this is possible with mlserver (more on this soon).
- With ray serve, you can hosts different models within the same instance and send the same request to both models with one 
API call. This can be quite useful for testing the output of two different models.

## Similarities
- Both tools allow you to create different replicas of a model in the same process.
- Both provide adaptive batching and online inference support.
- You can create inference graphs with either
- Both tools have integrations with different key libraries such as XGBoost, although mlserver has more integrations available.