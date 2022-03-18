---
description: Start your local Fief instance in a few minutes.
---

# Quickstart

We provide a [Docker](https://www.docker.com/get-started) image to help you start the Fief server locally in no time!

Run the following command:

```bash
    docker run --rm ghcr.io/fief-dev/fief:latest fief quickstart --docker
```

The result of this command is a complete **`docker run` command** with the required **secrets** generated to help you get started. It'll look like the following:

```bash
docker run \
  --name fief-server \
  -d \
  -e "SECRET=XXX" \
  -e "FIEF_CLIENT_ID=XXX" \
  -e "FIEF_CLIENT_SECRET=XXX" \
  -e "ENCRYPTION_KEY=XXX" \
  ghcr.io/fief-dev/fief:latest
```

{% hint style="warning" %}
**Save those secrets somewhere safe!**

If you need restart or recreate your container, you'll probably need to set the same secrets again. If you lose them, you'll likely lose access to data or have a bad configuration.

[Read more about secrets and environment variables](environment-variables.md)
{% endhint %}

### Create main workspace

Next, you'll need to create the main **workspace**. Simply run the following command:

```bash
docker exec fief-server fief create-main-workspace
```

You should see the following output:

```
Main Fief workspace created
```

### Create admin user

Finally, you need to create an **admin user** for this main workspace that'll have access to the management dashboard. Run the following command:

```bash
docker exec -it fief-server fief create-main-user --user-email anne@bretagne.duchy
```

{% hint style="info" %}
Of course, make sure to replace `--user-email` value with your own email address!
{% endhint %}

You'll then be prompted for a password. If everything goes well, you should see the following output:

```
Main Fief user created
```
