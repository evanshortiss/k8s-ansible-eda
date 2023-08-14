FROM quay.io/rhdevelopers/ansible-eda-base:latest

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
