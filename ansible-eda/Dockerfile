FROM quay.io/rhdevelopers/ansible-eda-base:latest

WORKDIR /eda-ansible

COPY inventory /eda-ansible/inventory
COPY k8s-eda-rule.yml /eda-ansible/k8s-eda-rule.yml
COPY k8s-eda-playbook.yml /eda-ansible/k8s-eda-playbook.yml
COPY k8s-eda-playbook-mail.yml /eda-ansible/k8s-eda-playbook-mail.yml

EXPOSE 5001

CMD ["ansible-rulebook", "--rulebook", "k8s-eda-rule.yml", "-i", "inventory", "--verbose"]