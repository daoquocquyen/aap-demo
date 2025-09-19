Log in to https://sandbox.redhat.com/

start openshift sandbox then AAP sandbox
Note: Eventually AAP will run on a assigned namespace (<username>-dev) on openshift sandbox

on AAP UI:
Automation Decision (Event Driven rulebooks):
  Projects -> Create Project -> name: aap-demo
    Organization: Default
    Source control type: Git
    Source Control URL: https://github.com/daoquocquyen/aap-demo.git
    Source Control Branch/Tag/Commit: main
  Decision Environments (image to run rulebook) -> Create Decision Environment
    name: aap-25-de
    Organization: Default
    Execution Environment: registry.redhat.io/ansible-automation-platform-25/de-supported-rhel8:latest

Automation Controller (Playbooks):
    Inventories -> Create Inventory -> name: EDA-Local
        Organization: Default
        Inventory Type: Smart
        Save
        Hosts -> Add Host -> name: localhost
        Variables:
            ansible_connection: local
            ansible_python_interpreter: /usr/bin/python3.11
    Projects -> Create Project -> name: aap-demo
        Organization: Default
        Execution Environment: default
        Source control type: Git
        Source Control URL: https://github.com/daoquocquyen/aap-demo.git
        Source Control Branch/Tag/Commit: main
    Execution Environments (image to run ansible) -> Create Execution Environment
        name: aws-xx
        organization: Default
        Execution Environment: quay.io/ansible/awx-ee:latest
    Templates -> Create Template -> name: restart-deployments-on-secret-modified
        Job Type: Run
        Inventory: EDA-Local
        Project: aap-demo
        Playbook: playbooks/restart-deployments-on-secret-modified.yml
        Execution Environment: aap-25-de
        Credential: EDA-Local
        Extra Variables:
          namespace: dqquyen2003asus-dev
