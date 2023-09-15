FROM postgres:16-bullseye

RUN apt-get update -y \
  && apt-get install -y --no-install-recommends awscli \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*
