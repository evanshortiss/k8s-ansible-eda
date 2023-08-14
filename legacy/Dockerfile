FROM registry.access.redhat.com/ubi9-minimal
RUN microdnf install java-17 python3 gcc python3-devel -y && microdnf clean all && python -m ensurepip --upgrade && pip3 install ansible ansible-rulebook asyncio aiokafka aiohttp aiosignal kubernetes requests ansible-runner wheel
ENV JAVA_HOME="/usr/lib/jvm/jre-17"

# Create ansible user and configure permissions on their homedir to support
# arbitrary user IDs https://docs.openshift.com/container-platform/4.13/openshift_images/create-images.html#use-uid_create-images
USER root

RUN groupadd --gid 1001 ansible \
  && useradd --uid 1001 --gid ansible --shell /bin/bash --create-home ansible

RUN chgrp -R 0 /home/ansible && \
    chmod -R g=u /home/ansible

# Use the ansible user, set ANSIBLE_HOME, and install dependencies etc in the
# ansible user's $HOME that supports arbitrary user IDs
USER ansible

ENV ANSIBLE_HOME=/home/ansible/.ansible
RUN ansible-galaxy collection install community.general ansible.eda kubernetes.core sabre1041.eda

WORKDIR /eda-ansible
COPY --chown=1001:1001 inventory /eda-ansible/inventory
COPY --chown=1001:1001 k8s-eda-rule.yml /eda-ansible/k8s-eda-rule.yml
COPY --chown=1001:1001 k8s-eda-playbook.yml /eda-ansible/k8s-eda-playbook.yml
COPY --chown=1001:1001 k8s-eda-playbook-mail.yml /eda-ansible/k8s-eda-playbook-mail.yml

EXPOSE 5001

CMD ansible-rulebook --rulebook k8s-eda-rule.yml -i inventory --verbose
