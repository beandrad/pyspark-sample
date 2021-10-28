ARG UBUNTU_VERSION="20.04"
FROM ubuntu:${UBUNTU_VERSION}

ARG PYTHON_VERSION=3.9
ARG SPARK_VERSION=3.2.0

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get -y install git unzip vim \
    curl zsh gnupg2 iputils-ping jq software-properties-common \
    # Clean up
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

# Install Python
RUN add-apt-repository ppa:deadsnakes/ppa \
    && apt-get update \
    && apt-get -y install python${PYTHON_VERSION} libpython${PYTHON_VERSION}-dev python3-pip python${PYTHON_VERSION}-dev python${PYTHON_VERSION}-venv gcc \
       build-essential libssl-dev libffi-dev python-dev libxml2-dev libxslt1-dev \
       libblas-dev libatlas-base-dev zlib1g-dev \
    && update-alternatives --install /usr/bin/python python /usr/bin/python${PYTHON_VERSION} 1 \
    && update-alternatives --install /usr/bin/python3 python3 /usr/bin/python${PYTHON_VERSION} 1 \
    && update-alternatives --install /usr/bin/pip pip /usr/bin/pip3 1

# Install Poetry
RUN curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python
ENV PATH=$PATH:/root/.poetry/bin
RUN poetry config virtualenvs.create false

# Install Python dependencies
COPY pyproject.toml pyproject.toml
RUN poetry install && rm pyproject.toml 

# Install Spark
RUN mkdir -p /tmp/docker-downloads \
    && apt-get -y install openjdk-8-jdk-headless wget \
    && wget https://downloads.apache.org/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop3.2.tgz -o /tmp/docker-downloads/spark-${SPARK_VERSION}-bin-hadoop3.2.tgz \
    && tar -xvzf /tmp/docker-downloads/spark-${SPARK_VERSION}-bin-hadoop3.2.tgz \
    && mv spark-${SPARK_VERSION}-bin-hadoop3.2 spark \
    && rm -rf /tmp/docker-downloads
ENV SPARK_HOME=/root/spark
ENV PATH=$PATH:$SPARK_HOME/bin

ARG DEBIAN_FRONTEND=interactive
