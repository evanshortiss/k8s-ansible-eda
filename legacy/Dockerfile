FROM registry.access.redhat.com/ubi9-minimal
RUN microdnf install java-17 python3 gcc python3-devel -y && microdnf clean all && python -m ensurepip --upgrade && pip3 install ansible ansible-rulebook asyncio aiokafka aiohttp aiosignal kubernetes requests ansible-runner wheel

# Configure Java and Ansible environment
ENV JAVA_HOME="/usr/lib/jvm/jre-17"
ENV ANSIBLE_HOME="/home/ansible/.ansible"
ENV ANSIBLE_CONFIG="/home/ansible/.ansible/ansible.cfg"

# Create ansible user and configure permissions on their homedir to support
# arbitrary user IDs https://docs.openshift.com/container-platform/4.13/openshift_images/create-images.html#use-uid_create-images
USER root
RUN groupadd --gid 1001 ansible \
  && useradd --uid 1001 --gid ansible --shell /bin/bash --create-home ansible

# Use the ansible user, set ANSIBLE_HOME, and install dependencies etc in the
# ansible user's $HOME that supports arbitrary user IDs
RUN ansible-galaxy collection install community.general ansible.eda kubernetes.core sabre1041.eda
RUN chown -R 1001:0 /home/ansible && \
    chmod -R ug+rwx /home/ansible

USER ansible
COPY --chown=1001:1001 ansible.cfg /home/ansible/.ansible/ansible.cfg

WORKDIR /eda-ansible
COPY --chown=1001:1001 inventory /eda-ansible/inventory
COPY --chown=1001:1001 k8s-eda-rule.yml /eda-ansible/k8s-eda-rule.yml
COPY --chown=1001:1001 k8s-eda-playbook.yml /eda-ansible/k8s-eda-playbook.yml
COPY --chown=1001:1001 k8s-eda-playbook-mail.yml /eda-ansible/k8s-eda-playbook-mail.yml

EXPOSE 5001

CMD ansible-rulebook --rulebook k8s-eda-rule.yml -i inventory -vvv
