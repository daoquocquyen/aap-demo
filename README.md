Log in to https://sandbox.redhat.com/

start openshift sandbox then AAP sandbox
Note: Eventually AAP will run on a assigned template on openshift sandbox

on AAP UI:
Automation Decision (Event Driven rulebooks):
  Projects -> Create Project -> name: aap-demo
    Organization: Default
    Source control type: Git
    Source Control URL: https://github.com/daoquocquyen/aap-demo.git
    Source Control Branch/Tag/Commit: main
  Decision Environments (image to run rulebook) -> Create Decision Environment -> name: aap-25-de
    Organization: Default
    Execution Environment: registry.redhat.io/ansible-automation-platform-25/de-supported-rhel8:latest

Automation Controller (Playbooks):
    Inventories -> Create Inventory -> name: EDA-Local
        Organization: Default
        Inventory Type: Smart
        Save
        Hosts -> Add Host -> name: localhost
    Projects -> Create Project -> name: aap-demo
        Organization: Default
        Execution Environment: default
        Source control type: Git
        Source Control URL: https://github.com/daoquocquyen/aap-demo.git
        Source Control Branch/Tag/Commit: main
    Templates -> Create Template -> name: secret-restart
        Job Type: Run
        Inventory: EDA-Local
        Project: aap-demo
        Playbook: playbooks/secret-restart.yaml
        Execution Environment: aap-25-de
        Credential: EDA-Local
        Save