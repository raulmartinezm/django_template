FROM python:3.12-rc-buster as base

ENV PYTHONUNBUFFERED=1

RUN python3 -m pip install --upgrade pip poetry==1.7.1

ENV POETRY_NO_INTERACTION=1

WORKDIR /app

COPY pyproject.toml poetry.lock README.md /app/
RUN poetry export --without-hashes --without=dev -f requirements.txt > requirements.txt

FROM python:3.12-rc-buster as runtime
ENV PYTHONUNBUFFERED=1

RUN apt-get update && apt-get install --no-install-recommends -y \
  libpq-dev \
  gettext \
  && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
  && rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY --from=base /app/requirements.txt /app/requirements.txt

RUN pip install -r requirements.txt && rm -rf requirements.txt
COPY docker/local/django/entrypoint.sh /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
