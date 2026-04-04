# syntax=docker/dockerfile:1

ARG PYTHON_BUILD_IMAGE=python:3.12-slim-bookworm
ARG PYTHON_RUNTIME_IMAGE=python:3.12-slim-bookworm

FROM ${PYTHON_BUILD_IMAGE} AS build

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    UV_NO_DEV=1

RUN python -m pip install --no-cache-dir uv

COPY . .
RUN uv sync --locked

FROM ${PYTHON_RUNTIME_IMAGE} AS runtime

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PATH="/app/.venv/bin:$PATH"

COPY --from=build /app /app

EXPOSE 8000
CMD ["gunicorn", "config.wsgi:application", "--bind", "0.0.0.0:8000", "--access-logfile", "-", "--error-logfile", "-", "--log-level", "info"]
