# ################################ target base ################################
FROM ubuntu:focal AS base

# Get the basic VIC dependencies
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update -q && \
    apt-get install -y -q --no-install-recommends \
        ca-certificates \
        build-essential \
        netcdf-bin \
        libnetcdf-dev \
        libopenmpi-dev \
        openmpi-bin \
        make

# Dedicated work directory for output
# Used by vic tests, not to be confused with the docker keyword WORKDIR
ENV WORKDIR /workdir
RUN mkdir -p $WORKDIR

# Put VIC in the container
COPY . /VIC/
WORKDIR /VIC

# Build the classic and image drivers
RUN cd /VIC/vic/drivers/classic && make
RUN cd /VIC/vic/drivers/image && make


# ################################ target test ################################
FROM base AS test

RUN apt-get update -q && \
    apt-get install -y -q \
    python3 \
    python3-pip \
    python-is-python3 \
    git \
    valgrind

# Install python packages
# We use pip because conda is too slow and doesn't play nice with containers
RUN pip install -r ci/requirements.txt

# Install VIC python driver
RUN python ./vic/drivers/python/setup.py install

# Get sample data
ENV SAMPLES_PATH /VIC_sample_data
RUN git clone https://github.com/UW-Hydro/VIC_sample_data.git $SAMPLES_PATH

# Run tests
CMD ./tests/run_tests.py unit examples \
    --classic=/VIC/vic/drivers/classic/vic_classic.exe \
    --data_dir=${SAMPLES_PATH} \
    --examples=/VIC/tests/examples/examples.cfg


# ################################ target vic ################################
# Put this target last to be the default
FROM base AS vic
# Command to run when this image is "run", just output the vic compile-time options
CMD /VIC/vic/drivers/classic/vic_classic.exe -o && \
    /VIC/vic/drivers/image/vic_image.exe -o
