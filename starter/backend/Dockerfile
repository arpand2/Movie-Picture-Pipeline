FROM public.ecr.aws/docker/library/python:3.10-alpine3.17

ENV FLASK_RUN_HOST=0.0.0.0

RUN apk update && \
    apk add --no-cache \
    pcre \
    pcre-dev \
    build-base \
    gcc \
    musl-dev \
    linux-headers \
    openssl \
    openssl-dev \
    libffi-dev \
    cargo \
    make

RUN adduser -u 1000 -D app

WORKDIR /app

RUN pip install --no-cache-dir pipenv

COPY Pipfile Pipfile.lock ./

# This line is the important change
RUN pipenv install --system --skip-lock

COPY . .

RUN mkdir -p /var/www/app

EXPOSE 5000

CMD ["uwsgi", "--http", ":5000", "--master", "--enable-threads", "-s", "/var/www/app/app.sock", "--manage-script-name", "--mount", "/=app:app"]