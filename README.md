# MedusaJS E-commerce Project

## Preface

Tried to install MedusaJS with connection to Supabase, but ran into error: `ETIMEOUT` during database migration. _(Suspect that it has got to do with Supabase free tier limitation)_

Refered to 2 tutorials to get Medusa running, using docker PostgreSQL db:

1. Installing MedusaJS and connecting it to docker PostgreSQL db [YouTube](https://youtu.be/sWjfAueW5NU?t=390).
2. Spinning up the app [YouTube](https://youtu.be/GgnxvcWOebk). (Didn't follow this guide much, just wanted some idea on what was going on)

## Prerequisite

To run this application you will need

- node >= 16.x.x installed
- docker desktop
- git

This project consist of 2 folders

- Medusa backend + admin portal
- Next.js storefront

## Installation Guide

First create a `docker.compose.yml` file with [these](https://github.com/LloydJanseVanRensburg/yt-medusajs-setup/blob/master/docker-compose.yml) contents

```yml
version: "3.9"
services:
  db:
    image: postgres
    volumes:
      - postgres-db:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=medusa_db
      - POSTGRES_USER=yourusername
      - POSTGRES_PASSWORD=yourpassword
    ports:
      - 5432:5432
volumes:
  postgres-db:
```

Ensure that docker with running

```Bash
docker ps
```

Then build the docker image to start the PostgreSQL database by running the following command in the root directory

```Bash
docker-compose up --build -d
```

Create a MedusaJS app following the [Medusa Setup Guide](https://docs.medusajs.com/create-medusa-app#step-1-run-create-medusa-app) or use the command below that runs a seed file and connects the app to the docker PostgreSQL database

```Bash
# RAW
npx create-medusa-app@latest --seed --db-url postgresql://<POSTGRES_USER>:<POSTGRES_PASSWORD>@<HOST>:5432/<POSTGRES_DB>

# Using values from docker-compose.yml
npx create-medusa-app@latest --seed --db-url postgresql://yourusername:yourpassword@localhost:5432/medusa_db
```

## Spinning up the app

> _Note: After creating Medusa app, a browser will automatically open up the admin page to setup a new admin user. However there was an error preventing me to create a new admin. Hence, I restarted the server without creating a admin account_

Start the backend server by running the following command in the backend directory

```Bash
yarn start
```

Then start the frontend server by running the following command in the frontend directory

```Bash
yarn dev
```

## Troubleshooting

**1. Admin account**

A browser page will automatically open up after the project was first successfully created. However I wasn't able to create the account and has to terminate the server.

When the backend server is manually restarted, the option to create another admin account is gone. Hence, use the account that was seeded found within `<Backend_folder> > data > seed.json > users`.

**2. Connecting Storefront with Backend on Github codespaces**

If a `network error` is displayed, check the networks tab in the browser's developer tools. It may be due to a CORS error.

Looking at the default `medusa-config.js` from the backend folder, the CORS variable `const STORE_CORS` is set to `process.env.STORE_CORS`, which is set to `localhost:<port>`.

Modify `process.env.STORE_CORS` to contain the IP address used by Github codespaces, eg: `http://127.0.0.1:<port>`. Then restart the backend server

## Connecting to Supabase

Haven't tried connecting the application to Supa base after setting it up to connect to a Docker PostgreSQL database. This may be a [hint](https://youtu.be/GgnxvcWOebk?t=531).

Not sure what these commands are here for anymore

```
curl 'http://localhost:9000/admin/regions' \
-H 'x-medusa-access-token: pk_01HJQNB453A5HV5A6YKGHM7HJ9'

curl -L -X GET 'http://localhost:9000/admin/regions' \g
-H 'Authorization: Bearer pk_01HJQNB453A5HV5A6YKGHM7HJ9'

curl -L -X GET 'http://127.0.0.1:9000/admin/regions' \
-H 'Authorization: Bearer pk_01HJQNB453A5HV5A6YKGHM7HJ9'
```
