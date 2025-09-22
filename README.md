Log in to https://sandbox.redhat.com/

start openshift sandbox then AAP sandbox
Note: Eventually AAP will run on a assigned namespace (<username>-dev) on openshift sandbox

On openshift UI:
    Click on User Menu (top right) -> Copy Login Command
On terminal:
    Paste the command and login
    oc project <username-dev>   # switch to your project
    oc apply -f k8s/deployment.yaml     # create a deployment to be restarted
    oc apply -f k8s/aap-restart-sa.yaml   # create service account
    oc create token restart-deploy-secret --duration=24h   # create a token for AAP to access k8s API




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
    Inventories -> Create Inventory ->
        Name: EDA-Local
        Organization: Default
        Inventory Type: Smart
        Hosts -> Add Host -> name: localhost
        Variables:
            ansible_connection: local
            ansible_python_interpreter: /usr/bin/python3.11
    Projects -> Create Project
        Name: aap-demo
        Organization: Default
        Execution Environment: default
        Source control type: Git
        Source Control URL: https://github.com/daoquocquyen/aap-demo.git
        Source Control Branch/Tag/Commit: main
    Execution Environments (image to run ansible) -> Create Execution Environment
        Name: aws-xx
        organization: Default
        Execution Environment: quay.io/ansible/awx-ee:latest
    Infrastructure -> Credentials -> Create Credential
        Name: aap-demo-token
        Organization: Default
        Credential Type: OpenShift or Kubernetes API Bearer Token
        Token: <the token created above>
        Host: https://kubernetes.default.svc:443
    Templates -> Create Template
        Name: restart-deployments-on-secret-modified
        Job Type: Run
        Inventory: EDA-Local
        Project: aap-demo
        Playbook: playbooks/restart-deployments-on-secret-modified.yml
        Execution Environment: aap-25-de
        Credential: aap-demo-token
        Extra Variables:
          namespace: <username-dev>   # namespace where the deployment to be restarted is located
