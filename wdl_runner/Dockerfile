# Copyright 2017 Google Inc.
#
# Use of this source code is governed by a BSD-style
# license that can be found in the LICENSE file or at
# https://developers.google.com/open-source/licenses/bsd

FROM openjdk:11-jre

# Install python
RUN apt-get update && \
    apt-get install python --yes && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install gcloud
# See https://cloud.google.com/sdk/
RUN curl https://sdk.cloud.google.com | bash

# Add the install location explicitly to the path (for non-interactive shells)
ENV PATH /root/google-cloud-sdk/bin:$PATH

# Install pip for the next two steps...
RUN curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py && \
    python get-pip.py

# Install Python "requests" (for cromwell_driver.py) package
RUN pip install requests simplejson

# Install Google Python client (for file_util.py) package
RUN pip install --upgrade google-api-python-client

# Remove pip
RUN pip uninstall pip -y && \
    rm get-pip.py

# Copy the wdl_runner python, shell script, and dependencies
RUN mkdir /wdl_runner
COPY cromwell_driver.py \
     file_util.py \
     sys_util.py \
     wdl_outputs_util.py \
     wdl_runner.py \
     wdl_runner.sh \
     /wdl_runner/
RUN chmod u+x /wdl_runner/wdl_runner.sh

# Copy Cromwell and the Cromwell conf template
RUN mkdir /cromwell
RUN cd /cromwell && \
    curl -L -O https://github.com/broadinstitute/cromwell/releases/download/68_hotfix_8e12ab5/cromwell-68-8e12ab5.jar
RUN ln /cromwell/cromwell-68-8e12ab5.jar /cromwell/cromwell.jar
COPY jes_template.conf /cromwell/

# Set up the runtime environment
ENV CROMWELL /cromwell/cromwell.jar
ENV CROMWELL_CONF /cromwell/jes_template.conf

WORKDIR /wdl_runner
