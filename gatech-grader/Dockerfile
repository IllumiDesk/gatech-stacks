ARG TAG=python-3.9.5
ARG BASE_IMAGE=illumidesk/gatech-notebook

FROM $BASE_IMAGE:$TAG

# update with grader user id and group id
ENV NB_UID=10001
ENV NB_GID=100

# install packages with mamba to improve build times
WORKDIR /tmp
COPY requirements.txt /tmp/requirements.txt

# the base jupyter/docker-stacks images take care of configuring
# pip + conda
RUN python3 -m pip install -r /tmp/requirements.txt

# enable nbgrader's create assignment and formgrader extensions
RUN jupyter nbextension enable --sys-prefix create_assignment/main \
 && jupyter nbextension enable --sys-prefix formgrader/main --section=tree \
 && jupyter serverextension enable --sys-prefix nbgrader.server_extensions.formgrader

# disable the assignment list extension, since graders don't need it
RUN jupyter serverextension disable --sys-prefix nbgrader.server_extensions.assignment_list \
 && jupyter nbextension disable --sys-prefix assignment_list/main --section=tree

# install and activate the async-nbgrader extensions
RUN jupyter nbextension install --sys-prefix --py async_nbgrader --overwrite \
 && jupyter nbextension enable --sys-prefix --py async_nbgrader \
 && jupyter serverextension enable --sys-prefix --py async_nbgrader

# fix permissions as root
USER root
RUN fix-permissions "${JULIA_PKGDIR}" \
 && fix-permissions "${HOME}" \
 && fix-permissions "${CONDA_DIR}"

# ensure we start user sessions with nb_user/nb_uid 
USER "${NB_USER}"

WORKDIR "${HOME}"
