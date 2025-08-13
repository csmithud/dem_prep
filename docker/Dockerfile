# Start from a core stack version
FROM jupyter/scipy-notebook:2022-01-03
#399cbb986c6b

LABEL authors="Curtis Smith"
LABEL maintainer="curtis.smith@Stantec.com"

COPY docker/stantec-ca.crt /usr/local/share/ca-certificates/stantec-ca.crt
COPY docker/stantec-ca.crt /etc/ssl/certs/stantec-ca.crt
COPY docker/wincacerts.pem /usr/local/share/ca-certificates/wincacerts.pem

COPY --chown=${NB_UID}:${NB_GID} docker/requirements.txt /tmp/

USER root
RUN cat /usr/local/share/ca-certificates/wincacerts.pem >> /etc/ssl/certs/stantec-ca.crt

USER $NB_USER

RUN pip config set global.cert /etc/ssl/certs/stantec-ca.crt

RUN conda config --set ssl_verify /etc/ssl/certs/stantec-ca.crt
ENV REQUESTS_CA_BUNDLE=/etc/ssl/certs/stantec-ca.crt
ENV PIP_CERT=/etc/ssl/certs/stantec-ca.crt


USER root


ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends --no-install-suggests \
    build-essential \
    grass \
    grass-dev \
	libgdal-dev \
    python3-distutils \
    subversion \
    ca-certificates \
    curl \
    apt-transport-https \
    lsb-release gnupg \
    && apt-get autoremove \
    && apt-get clean

RUN NAME=$(gdal-config --version)
RUN apt-get update && apt-get install -y default-jdk 

RUN chmod -R a+rwx $HOME
RUN chown -R $NB_USER .



RUN chmod 644 /usr/local/share/ca-certificates/stantec-ca.crt \
	&& chmod 644 /usr/local/share/ca-certificates/wincacerts.pem \
    && chmod 644 /etc/ssl/certs/stantec-ca.crt \
    && update-ca-certificates

# switch the user
USER $NB_USER

ENV REQUESTS_CA_BUNDLE=/etc/ssl/certs/stantec-ca.crt
ENV PIP_CERT=/etc/ssl/certs/stantec-ca.crt


RUN conda config --set ssl_verify /etc/ssl/certs/stantec-ca.crt

RUN pip config set global.cert /etc/ssl/certs/stantec-ca.crt

RUN npm config set cafile /etc/ssl/certs/stantec-ca.crt --global

RUN pip install -r /tmp/requirements.txt && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

RUN pip install pygdal==3.0.4.11
RUN pip install pyogrio
RUN pip install rtree
RUN pip install pygeos
RUN pip install boto3

RUN pip install git+https://github.com/zarch/grass-session.git && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

#switch the user
USER root

RUN npm config set cafile /etc/ssl/certs/stantec-ca.crt --global
