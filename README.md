# Python Google Cloud Pub/Sub sample for Google App Engine Standard Environment

<!--
[![Open in Cloud Shell][shell_img]][shell_link]

[shell_img]: http://gstatic.com/cloudssh/images/open-btn.png
[shell_link]: https://console.cloud.google.com/cloudshell/open?git_repo=https://github.com/GoogleCloudPlatform/python-docs-samples&page=editor&open_in_editor=appengine/standard/pubsub/README.md
 -->

This demonstrates how to send and receive messages using [Google Cloud Pub/Sub](https://cloud.google.com/pubsub) on [Google App Engine Standard Environment](https://cloud.google.com/appengine/docs/standard/).

## Setup

Before you can run or deploy the sample, you will need to do the following:

1. Enable the Cloud Pub/Sub API in the [Google Developers Console](https://console.developers.google.com/project/_/apiui/apiview/pubsub/overview).

2. Create a topic and subscription.
```bash
$ gcloud pubsub topics create [your-topic-name]
$ gcloud pubsub subscriptions create [your-subscription-name] \
    --topic [your-topic-name] \
    --push-endpoint \
        https://[your-app-id].appspot.com/pubsub/push?token=[your-token] \
    --ack-deadline 30
```

3. Update the environment variables in ``app.yaml``.

## Running locally

<!-- Refer to the [top-level README](../README.md) for instructions on running and deploying. -->

When running locally, you can use the [Google Cloud SDK](https://cloud.google.com/sdk) to provide authentication to use Google Cloud APIs:

```bash
$ gcloud init
```

Install dependencies, preferably with a virtualenv:

```bash
$ virtualenv env
$ source env/bin/activate
$ pip install -r requirements.txt
```

Then set environment variables before starting your application in one of the following ways:

1. By exporting environment variable in terminal
```bash
$ export SECRET_KEY=[your-secret-key]
$ export GOOGLE_CLOUD_PROJECT=[your-project-name]
$ export PUBSUB_VERIFICATION_TOKEN=[your-verification-token]
$ export PUBSUB_TOPIC=[your-topic]
```
2. Or, By creating `.env` file:
```
SECRET_KEY=[your-secret-key]
GOOGLE_CLOUD_PROJECT=[your-project-name]
PUBSUB_VERIFICATION_TOKEN=[your-verification-token]
PUBSUB_TOPIC=[your-topic]
```

Now, you can run the server as follows for local testing:
```bash
$ python main.py
```

### Simulating push notifications

The application can send messages locally, but it is not able to receive push messages locally. You can, however, simulate a push message by making an HTTP request to the local push notification endpoint. There is an included ``sample_message.json``. You can use
``curl`` or [httpie](https://github.com/jkbrzt/httpie) to POST this:

```bash
$ curl -X POST 127.0.0.1:8080/pubsub/push?token=[your-token] \
        --data @sample_message.json \
        --header "Content-Type: application/json"
```

Or
```bash
$ http POST ":8080/pubsub/push?token=[your-token]" < sample_message.json
```

Response:
```bash
HTTP/1.0 200 OK
Content-Length: 2
Content-Type: text/html; charset=utf-8
Date: Mon, 10 Aug 2015 17:52:03 GMT
Server: Werkzeug/0.10.4 Python/2.7.10

OK
```

After the request completes, you can refresh ``localhost:8080`` and see the message in the list of received messages.

## Running on App Engine

Deploy using `gcloud`:
```bash
$ gcloud app deploy app.yaml
```

You can now access the application at `https://your-app-id.appspot.com`. You can use the form to submit messages, but it's non-deterministic which instance of your application will receive the notification. You can send multiple messages and refresh the page to see the received message.
