# Sentry

`Dockerfile` + sample `docker-compose.yml` to run `Sentry` (older versions) with Docker.

**Important note**: take to account, that I'm new to Docker and DevOps is not my expertise, 
so please do not consider content of this repository as an example of best (or even good) 
practices.

## Why?

While [Sentry.io](https://sentry.io) provides possibility to run a self-hosted `Sentry` in a Docker container,
it may not work for everyone, because:

1. They provide an 'all-on-one' container
2. System requirements of this container are enormous (4×CPU / 8Gb RAM / 20Gb disk space)
3. Modern Intel CPU (with SSE4 instructions set) is a strict requirement, so _no ARM CPUs_ :(

In this repository I shared results of my attempts to run older `Sentry` versions, with much
smaller footprint, which still could be useful for anyone, who needs just error logging, aggregation
and tracking, without all other stuff, provided by the latest-and-greatest `Sentry`.

Main goal was to be able to have a self-hosted `Sentry` at my free-tier Oracle Cloud VPS with
ARM Graviton CPUs. As a bonus, this image also could run at Apple Silicon.

Of course, the image should also run without issues on any architecture, supported by the
base image, which included x86-64. 

## Setup

#### Overview
1. Fetch or copy repository to your Docker host
2. Create `.env` file
3. Update `system.secret-key`
4. Build project
5. Start PostgreSQL service
6. Run Sentry migrations
7. Run project

### 1. Fetch or copy repository to your Docker host

Fetch this repo with `git clone` or download and unpack files at your Docker host.

### 2. Create `.env` file

Run:
```
cp example.env .env
```

Edit credentials (at least set more safe `POSTGRES_PASSWORD`).

### 3. Update `system.secret-key`

Edit `sentry/config/config.yml` file and change `system.secret-key` value.

**Note**: It's extremely unconvenient and unsafe part os this setup and should be changed
to something better. Probably I'll fix it in future versions. Feel free to create PRs with
suggestions.

### 4. Build project

```
docker-compose build
```

### 5. Start PostgreSQL service

At the next step we'll run Sentry migrations, which require PostgreSQL to be up and running.
Though, on the first run of PostgreSQL container, it will take some time (under a half of 
minute) to create an empty database. So, start the service manually and give it some time 
to initialize:

```
docker-compose up -d postgres
```

### 6. Run Sentry migrations

```
docker-compose run --rm -it sentry_web upgrade
```

This command will run Sentry (Django) migrations on a database. After migrations are applied,
it will ask you about user account creation. Follow the dialog:

```
...
 > sentry:0270_auto__add_field_organizationmember_token
 > sentry:0271_auto__del_field_organizationmember_counter
Created internal Sentry project (slug=internal, id=1)

Would you like to create a user account now? [Y/n]: y
Email: address@email.com
Password: 
Repeat for confirmation: 
Should this user be a superuser? [y/N]: y <= IMPORTANT!
...
```

### 7. Run the project

```
docker-compose up -d
```

With default settings, `Sentry` will be available at port `9000`. Log in with 
the credentials you've set at migrations step, set up email settings, create
a new Sentry project and have fun!