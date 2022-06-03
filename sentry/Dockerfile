# FROM --platform=linux/arm/v8 ubuntu:16.04
FROM ubuntu:16.04

WORKDIR /root

RUN apt-get update && apt-get install -y \
    python-pip \
    python-dev \
    python-psycopg2 \
    libxslt1-dev \
    libxml2-dev \
    libz-dev \
    libffi-dev \
    libssl-dev \
    libpq-dev \
    libyaml-dev \
    && rm -rf /var/lib/apt/lists/*

# specific versions of these libs must be installed because
# of missing top version limits in Sentry dependencies
RUN pip install lxml==3.6.0
RUN pip install cryptography==2.7 pyOpenSSL==0.14 sqlparse==0.2.4 redis==2.10.5 uWSGI==2.0.12
RUN pip install sentry==8.3.3 sentry-slack


COPY config/* /root/.sentry/

# RUN sentry django collectstatic --noinput

EXPOSE 9000

ENTRYPOINT ["sentry", "start", "http"]