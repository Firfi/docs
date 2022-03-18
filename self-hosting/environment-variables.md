---
description: List of environment variables available and how to set them.
---

# Environment variables

Fief server relies heavily on environment variables for configuration. You'll likely need to adjust those settings for your deployment.

## Set environment variables

### Using `docker run`

When running Fief server with Docker, the most straightforward way is to use the `-e` option on the command line, as shown in the [Quickstart](quickstart.md) section.

```bash
docker run \
  --name fief-server \
  -p 8000:80
  -d \
  -e "SECRET=XXX" \
  -e "FIEF_CLIENT_ID=XXX" \
  -e "FIEF_CLIENT_SECRET=XXX" \
  -e "ENCRYPTION_KEY=XXX" \
  ghcr.io/fief-dev/fief:latest
```

However, it may become hard to maintain when having lot of variables to set. An alternative way is to use a `.env` file. It's a simple file where each line consists of a key and a value separated by an equal sign:

{% code title=".env" %}
```systemd
SECRET=XXX
FIEF_CLIENT_ID=XXX
FIEF_CLIENT_SECRET=XXX
ENCRYPTION_KEY=XXX
```
{% endcode %}

Then, you can reference this file in the Docker command:

```bash
docker run \
  --name fief-server \
  -p 8000:80
  -d \
  --env-file .env \
  ghcr.io/fief-dev/fief:latest
```

### Using Docker Compose

For more complex setups, you may need to configure a Docker Compose file to help you manage all your containers. You can directly define your environment variables in the Compose file.

You'll find below an example of a Docker Compose file to run the Fief server.

```yaml
version: "3.9"

services:
  fief:
    image: ghcr.io/fief-dev/fief:latest
    ports:
      - "80:8000"
    environment:
      - SECRET=XXX
      - FIEF_CLIENT_ID=XXX
      - FIEF_CLIENT_SECRET=XXX
      - ENCRYPTION_KEY=XXX
```

## Reference

For each variable, we'll try to provide a sensible example value to help you configure it correctly. Throughout the examples, we'll assume that you host your Fief server on the sub-domain `fief.bretagne.duchy`.

### General

| Name                 | Description                                                                                     | Default                     | Allowed values                   | Example                      |
| -------------------- | ----------------------------------------------------------------------------------------------- | --------------------------- | -------------------------------- | ---------------------------- |
| `ENVIRONMENT`        | Name of the deployment environment                                                              | development                 | development, staging, production | production                   |
| `LOG_LEVEL`          | Log verbosity                                                                                   | DEBUG                       | DEBUG, INFO, WARNING, ERROR      | INFO                         |
| `ROOT_DOMAIN`        | Root domain where your server will be running. Mainly used for generating workspace subdomains. | localhost                   |                                  | bretagne.duchy               |
| `ALLOW_ORIGIN_REGEX` | Regex used to control CORS access to your API                                                   | http://.\*localhost:\[0-9]+ |                                  | https://.\*\\.bretagne.duchy |

### Secrets

| Name             | Description                                                                     | Default | Allowed values                      | Example |
| ---------------- | ------------------------------------------------------------------------------- | ------- | ----------------------------------- | ------- |
| `SECRET`         | Secret value used to sign reset password tokens.                                |         | Any sufficiently long string        |         |
| `ENCRYPTION_KEY` | Key used to encrypt the external databases credentials inside the main database |         | A valid Fernet key encoded in UTF-8 |         |

### Database

| Name                | Description                                                  | Default                   | Allowed values            | Example      |
| ------------------- | ------------------------------------------------------------ | ------------------------- | ------------------------- | ------------ |
| `DATABASE_TYPE`     | Type of the main database                                    | SQLITE                    | POSTGRESQL, MYSQL, SQLITE | POSTGRESQL   |
| `DATABASE_HOST`     | Host of the main database                                    |                           |                           | localhost    |
| `DATABASE_PORT`     | Listening port of the main database                          |                           |                           | 5432         |
| `DATABASE_USERNAME` | Main database user                                           |                           |                           | fief         |
| `DATABASE_PASSWORD` | Main database user's password                                |                           |                           | fiefpassword |
| `DATABASE_NAME`     | Main database name                                           | fief.db                   |                           | fief         |
| `DATABASE_LOCATION` | For SQLite databases, path where to store the database files | Current working directory |                           |              |

More details about how to setup a database in the dedicated section.

{% content-ref url="setup-database.md" %}
[setup-database.md](setup-database.md)
{% endcontent-ref %}

### Redis

We use a Redis instance to manage background jobs (send emails, heavy computations...). A Redis instance is already up-and-running in the official Docker image, but you can provide your own one if needed.

| Name         | Description           | Default                | Allowed values | Example |
| ------------ | --------------------- | ---------------------- | -------------- | ------- |
| `REDIS_URL`  | URL of a Redis server | redis://localhost:6379 |                |         |

### Email provider

| Name                    | Description                                    | Default | Allowed values | Example                      |
| ----------------------- | ---------------------------------------------- | ------- | -------------- | ---------------------------- |
| `EMAIL_PROVIDER`        | Type of email provider                         | NULL    | NULL, POSTMARK | POSTMARK                     |
| `EMAIL_PROVIDER_PARAMS` | Configuration dictionary of the email provider | {}      |                | {"server\_token": "XXX-XXX"} |

More details about how to setup an email provider in the dedicated section.

{% content-ref url="setup-email-provider.md" %}
[setup-email-provider.md](setup-email-provider.md)
{% endcontent-ref %}

### Login session

A login session is a cookie used to maintain the state of the login flow of a user, from the login page until they're redirected to your application.

| Name                          | Description                             | Default              | Allowed values | Example |
| ----------------------------- | --------------------------------------- | -------------------- | -------------- | ------- |
| `LOGIN_SESSION_COOKIE_NAME`   | Name of the login session cookie        | fief\_login\_session |                |         |
| `LOGIN_SESSION_COOKIE_DOMAIN` | Domain of the login session cookie      | _Empty string_       |                |         |
| `LOGIN_SESSION_COOKIE_SECURE` | Secure flag of the login session cookie | True                 |                |         |

### Session

A session is a cookie used to maintain the session of a user **on the Fief authentication pages**. It's different from the session you'll maintain in your own application.

Its purpose is to allow a user to re-authenticate quickly to your app without having them to input their credentials again.

| Name                       | Description                               | Default                 | Allowed values | Example |
| -------------------------- | ----------------------------------------- | ----------------------- | -------------- | ------- |
| `SESSION_COOKIE_NAME`      | Name of the session cookie                | fief\_session           |                |         |
| `SESSION_COOKIE_DOMAIN`    | Domain of the session cookie              | _Empty string_          |                |         |
| `SESSION_COOKIE_SECURE`    | Secure flag of the session cookie         | True                    |                |         |
| `SESSION_LIFETIME_SECONDS` | Lifetime of the session cookie in seconds | 86400 \* 30 _(30 days_) |                |         |

### Fief-ception

Fief-ception is a mind-fucking concept describing the fact that we actually use Fief to authenticate Fief users to the app 🤯

That's why we necessarily need to create a first workspace and an admin user before being able to use Fief, as described in the [Quickstart](quickstart.md) section.

The variables below are here to configure the Fief server with a proper Fief client, as you would do in your own application!

| Name                  | Description                                      | Default          | Allowed values | Example                     |
| --------------------- | ------------------------------------------------ | ---------------- | -------------- | --------------------------- |
| `FIEF_DOMAIN`         | Domain of your main Fief workspace               | localhost        |                | fief.bretagne.duchy         |
| `FIEF_BASE_URL`       | URL of the main Fief workspace. It calls itself! | http://localhost |                | https://fief.bretagne.duchy |
| `FIEF_CLIENT_ID`      | Client ID in your main Fief workspace            |                  |                |                             |
| `FIEF_CLIENT_SECRET`  | Client secret in your main Fief workspace        |                  |                |                             |
| `FIEF_ENCRYPTION_KEY` | Optional RSA key used to encrypt the JWT tokens  |                  |                |                             |

### Admin session

An admin session is a cookie used to maintain the session of a user on the Fief admin dashboard.

| Name                               | Description                             | Default              | Allowed values | Example |
| ---------------------------------- | --------------------------------------- | -------------------- | -------------- | ------- |
| `FIEF_ADMIN_SESSION_COOKIE_NAME`   | Name of the admin session cookie        | fief\_admin\_session |                |         |
| `FIEF_ADMIN_SESSION_COOKIE_DOMAIN` | Domain of the admin session cookie      | _Empty string_       |                |         |
| `FIEF_ADMIN_SESSION_COOKIE_SECURE` | Secure flag of the admin session cookie | True                 |                |         |

