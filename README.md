## GPT-2 based text generator (API part)

Text generator based on GPT-2. Text generator consists of 2 parts: 
- Web interface used to modify generator settings (another repository)
- API hosted on Google Cloud Run that does runs GPT-2 and generates text. (this repository)

This is API part of the system. Web interface part can be found here:
https://github.com/objectis/ai-gpt2generator-flask

### How to Build the Container And Start Cloud Run

Since Cloud Run is stateless without access to local storage, you must bundle the model within the container. First, download/clone this repo and copy the model into the folder (the model should be in the form of the folder hierarchy `/checkpoint/run1`, which is the case by default for most finetuning scripts)

Then build the image:

```shell
docker build . -t gpt2
```

If you want to test the image locally with the same specs as Cloud Run, you can run:

```shell
docker run -p 8080:8080 --memory="2g" --cpus="1" gpt2
```

You can then visit/`curl` http://0.0.0.0:8080 to get generated text!

Then, tag the image and upload it to the Google [Container Registry](https://console.cloud.google.com/kubernetes/images/list) (note, this will take awhile due to the image size!):

```shell
docker tag gpt2 gcr.io/[PROJECT-ID]/gpt2
docker push gcr.io/[PROJECT-ID]/gpt2
```

Once done, deploy the uploaded image to Cloud Run via [the console](https://console.cloud.google.com/run). **Set Memory Allocated to 2 GB and Maximum Requests Per Container to 1**!

### See it in action
Deployed image can be viewed here: https://gpt2-bq5jveycyq-ew.a.run.app/

### NOTES
Few things to keep in mind: 
- Although OpenAI has released all models, we are currently running API for smallest (117M) model since only it can be deployed within free limitations of Google Cloud Run
- Bigger models can be included and deployed in the same manner, however it requires to pay for Google Cloud Run services
