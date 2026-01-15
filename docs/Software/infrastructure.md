# Core Infrastructure Software

These are the core services/applications that manage and control the infrastructure.

## Ansible Automation Platform

AAP is used for configuration management and automation like OS updates, VM post-deployment tasks, and testing new automations.

AAP is hosted on a RHEL VM via Podman.

!!! note

    A minimal inventory file was used for install/is used during upgrades, so the various components (controller, automation hub, gateway, etc.) are all deployed on this single node.

## GitLab

GitLab (self-managed EE) is used to house my Git repositories and CI/CD pipelines.

GitLab is hosted on an Alma Linux VM via the omnibus package.

An Alma VM with the GitLab runner installed also exists for pipeline tasks to be delegated to.

## Netbox

Netbox is used for DCIM (Datacenter Infrastructure Management) and IPAM (IP Address Management).

DCIM includes mapping of my physical cables, interfaces, patch panels, connections, hardware, etc.

IPAM includes defining IP prefixes, pools, VLANs, etc.

Netbox is integrated with Terraform to automatically fetch an available IP address in the given prefix and self-document the deployed resource.

Netbox is hosted using the [Netbox docker container](https://github.com/netbox-community/netbox-docker).

## Terraform

Terraform is used for defining my infrastructure (mostly the VMs) as code. Each VM resource lives in my repository and can be changed in code rather than editing inside the UI.

This allows for a single source-of-truth for my VMs (the code). Changes occur here and are applied via Terraform, avoiding cumbersome click-ops within the UI.

## Traefik

Traefik is used as a reverse proxy and load balancer. Traefik is configured with ACME/LetsEncrypt for automatic SSL certificate management.

Traefik is hosted on 3 VM nodes and uses `keepalived` and `lsyncd` for high availability.

Config changes occur on the first node and get replicated to the other 2 nodes. My DNS server points all service domains to the keepalived VIP, which means if a node is down, another one will take over and serve the request(s).

Config changes are made via a GitLab pipeline.

??? example "Pipeline Workflow Example"

    1. A new HTTP router and service are added to Traefik's dynamic config directory inside the repository.
    2. The changes are committed and pulled into the main branch, which triggers the pipeline.
    3. The pipeline syncs the contents of the repository's dynamic config folder to the remote Traefik VM.
    4. Traefik watches the dynamic config directory for changes and the new HTTP router/service are picked up/applied.

??? example "DNS Example"

    `10.0.0.3` is the keepalived VIP.

    If node 1 (10.0.0.4) is up, it responds to 10.0.0.3.
    If node 1 is down, node 2 (10.0.0.5) takes over for 10.0.0.3.
    If node 1 **and** node 2 are down, node 3 takes over for 10.0.0.3.

    Wildcard A record: `*.home.42u.dev` -> `10.0.0.3`. This means _anything_.home.42u.dev will be sent to the Traefik "cluster".

## Packer

Packer is used to automate the build process for VM templates (for Windows Server and Alma Linux).

This is set up via a GitLab pipeline. Any time there is a change to the packer template code, GitLab triggers a pipeline to run packer and rebuild the template.

For Linux: updates, SSH keys, base package installs, etc. are performed within packer. When the template is deployed to a new VM, these settings are already in-place.

For Windows: things like updates, WinRM configuration, and other modifications take place during packer's build process.

No need to manually create a new VM, install Windows, run Windows Updates, run PowerShell commands, then sysprep. Packer handles all of this and allows for the deployed VM (from the packer-generated template) to be customized (e.g. assigning it an IP address and hostname) when deployed.

## Vault

Hashicorp Vault is used to centrally store and manage secrets. SSH keys, API tokens, service accounts, etc. live here and are encrypted at rest.

Integrations for Ansible Automation Platform and Terraform, as an example, can be leveraged to automate secret retrieval.

This eliminates the need to store secrets in a `.env` file, or even worse, plain text.

!!! info "Reminder"

    This is a friendly reminder to check your repos/codebase for any exposed secrets!

Vault is hosted on 3 VM nodes using the integrated Raft storage component for a highly available cluster. Traefik load balances the API requests between the nodes. In this particular case, Traefik operates in passthrough mode, so true end-to-end encryption is achieved. SSL is terminated on each node instead of handled by Traefik. 

## Veeam

Veeam is used to backup VMs and PCs. Each backup has 2 copies stored on-site, while one is sent off-site for critical resources.

VMs are backed up using vSphere tags. This allows for easier management and standardization of backups.

??? example

    1. Ansible VM is backed up with Veeam to the primary backup target.
    2. A backup copy job copies this backup from the primary target to the secondary target.
    3. Another backup copy job copies this backup to an offsite backup target.

    Ansible VM has a "Daily" tag attached to it.
    Veeam's "Daily" job backs up any VMs with the "Daily" tag.

Veeam is hosted on a Windows Server VM. This will soon be migrated to the new Linux-based software appliance.

## Zabbix

Zabbix is used to monitor the lab's hardware and services.

Zabbix is hosted on an Alma Linux VM, using the bare-metal installation.

Most hardware is monitored via SNMP or native API templates. This includes equipment like switches, UPSes, and Dell iDRACs.

At the OS level, the Zabbix agent is utilized to pull deeper metrics and expose greater alerting capabilities.

Certain services and misc. resources (e.g. SSL certificates) are also monitored.

All alerts are sent to Pushover.