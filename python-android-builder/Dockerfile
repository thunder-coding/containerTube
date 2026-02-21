FROM ubuntu:24.04

# Install necessary packages we need to build Python
RUN : && \
    # First update our package lists, and update our packages to not do a partial upgrade.
    apt-get update && apt-get full-upgrade -y && \
    apt-get install -y \
    # Needed for getting the sources and SDK
    git \
    curl \
    unzip \
    # For actually building Python
    build-essential \
    python-is-python3 \
    autoconf \
    autoconf-archive \
    m4 \
    autotools-dev \
    libffi-dev \
    # Needed for Android command line tools
    openjdk-21-jdk && \
    # Get rid of the apt cache to save space in the image.
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Switch to a non-root user to do the rest of our work, as we don't want to be building Python as root.
USER ubuntu

# Setup Android command line tools needed by Python to build
ENV ANDROID_HOME=/home/ubuntu/.android
ARG ANDROID_COMMAND_LINE_TOOLS_VERSION=14742923
ARG ANDROID_COMMAND_LINE_TOOLS_SHA1=48833c34b761c10cb20bcd16582129395d121b27

RUN : && \
    mkdir $ANDROID_HOME && \
    curl -o /tmp/cmdline-tools.zip https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_COMMAND_LINE_TOOLS_VERSION}_latest.zip && \
    echo "${ANDROID_COMMAND_LINE_TOOLS_SHA1}  /tmp/cmdline-tools.zip" | sha1sum -c - && \
    mkdir -p $ANDROID_HOME/cmdline-tools && \
    cd $ANDROID_HOME/cmdline-tools && \
    unzip /tmp/cmdline-tools.zip -d . && \
    mv cmdline-tools/ latest/ && \
    rm /tmp/cmdline-tools.zip


# Now clone the Python repository and build Python.
# Point to our fork as that's what we will be using to make our changes and test them.
ARG GIT_REPO=https://github.com/thunder-coding/cpython.git
ARG GIT_BRANCH=android-pkgconfig
RUN : && \
    git clone --branch $GIT_BRANCH $GIT_REPO /home/ubuntu/cpython && \
    cd /home/ubuntu/cpython
WORKDIR /home/ubuntu/cpython
