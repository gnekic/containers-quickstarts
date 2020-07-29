FROM quay.io/openshift/origin-jenkins-agent-base:4.4

LABEL com.redhat.component="jenkins-agent-ansible-ubi7-docker" \
      name="openshift/origin-jenkins-agent-ansible-ubi7" \
      version="4.4" \
      architecture="x86_64" \
      release="1" \
      io.k8s.display-name="Jenkins Agent Ansible" \
      io.k8s.description="The jenkins agent ansible image has ansible on top of the jenkins agent base image." \
      io.openshift.tags="openshift,jenkins,agent,ansible"


ADD ubi.repo /etc/yum.repos.d/ubi.repo

ENV PACKAGES="\
docker \
git \
openssh-client \
ruby \
ansible \
ansible-lint \
docker-py \
libvirt \
rsync \
py3-bcrypt \
py3-botocore \
py3-certifi \
py3-cffi \
py3-chardet \
py3-click \
py3-colorama \
py3-cryptography \
py3-docutils \
py3-flake8 \
py3-idna \
py3-jinja2 \
py3-mccabe \
py3-netifaces \
py3-paramiko \
py3-pbr \
py3-pexpect \
py3-pip \
py3-pluggy \
py3-psutil \
py3-ptyprocess \
py3-py \
py3-pycodestyle \
py3-pynacl \
py3-pytest \
py3-requests \
py3-ruamel \
py3-setuptools \
py3-urllib3 \
py3-virtualenv \
py3-websocket-client \
python3 \
"

ENV BUILD_DEPS="\
gcc \
libc-dev \
libvirt-dev \
make \
ruby-dev \
ruby-rdoc \
"

ENV PIP_INSTALL_ARGS="\
--only-binary :all: \
--no-index \
-f /usr/src/molecule/dist \
"

ENV GEM_PACKAGES="\
rubocop \
json \
etc \
"

ENV MOLECULE_EXTRAS="docker,docs,windows,lint"

ENV MOLECULE_PLUGINS="\
molecule-azure \
molecule-containers \
molecule-digitalocean \
molecule-ec2 \
molecule-gce \
molecule-hetznercloud \
molecule-libvirt \
molecule-lxd \
molecule-openstack \
molecule-vagrant \
"

RUN \
    apk add --update --no-cache \
    ${BUILD_DEPS} ${PACKAGES} \
    && gem install ${GEM_PACKAGES} \
    && apk del --no-cache ${BUILD_DEPS} \
    && rm -rf /root/.cache
COPY --from=molecule-builder \
    /usr/src/molecule/dist \
    /usr/src/molecule/dist
RUN \
    python3 -m pip install \
    ${PIP_INSTALL_ARGS} \
    "molecule[${MOLECULE_EXTRAS}]" testinfra ${MOLECULE_PLUGINS} && \
    molecule --version && \
    molecule drivers

ADD scl_enable /usr/share/container-scripts/
ENV BASH_ENV=/usr/share/container-scripts/scl_enable \
    ENV=/usr/share/container-scripts/scl_enable \
    PROMPT_COMMAND="./usr/share/container-scripts/scl_enable" \
    PATH="${PATH}:/opt/rh/rh-python36/root/usr/bin"

USER 1001
