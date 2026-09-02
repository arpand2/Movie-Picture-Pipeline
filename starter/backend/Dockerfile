FROM public.ecr.aws/docker/library/python:3.10-alpine3.17

ENV FLASK_RUN_HOST=0.0.0.0

# Install system dependencies required to compile uwsgi and other packages
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

# Create non-root user
RUN adduser -u 1000 -D app

WORKDIR /app

# Install pipenv
RUN pip install --no-cache-dir pipenv

# Copy dependency files first (better for caching)
COPY Pipfile Pipfile.lock ./

# Install Python dependencies
# Using --ignore-pipfile to avoid lock file platform issues
RUN pipenv install --system --deploy --ignore-pipfile

# Copy the rest of the application
COPY . .

# Create directory for socket
RUN mkdir -p /var/www/app

EXPOSE 5000

CMD ["uwsgi", "--http", ":5000", "--master", "--enable-threads", "-s", "/var/www/app/app.sock", "--manage-script-name", "--mount", "/=app:app"]