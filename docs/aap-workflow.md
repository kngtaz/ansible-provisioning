# AAP workflow configuration outline

This is a design outline, not an importable Controller export. No templates are created automatically.

Create a Project pointing to this repository after publishing it to your approved SCM.
Create a localhost inventory and select an approved execution environment.
Create one Job Template for each playbook below and allow the `request` extra variable
through the approved ServiceNow integration. Keep credentials in AAP credential objects.
ServiceNow performs owner and infrastructure approvals before launching the workflow;
this skeleton does not authorize requests or implement the ServiceNow API integration.

| Node | Playbook | Success | Failure |
|---|---|---|---|
| Job 1 | playbooks/01_validate_request.yml | Job 2 | Failure callback; infrastructure admin |
| Job 2 | playbooks/02_check_rhel_entitlements.yml | Job 3 | Failure callback; service owner |
| Job 3 | playbooks/03_provision_instances.yml | Job 4A | Failure callback; infrastructure admin |
| Job 4A | playbooks/04a_post_provision.yml | Job 4B | Failure callback; infrastructure admin |
| Job 4B | playbooks/04b_verify_provisioning.yml | Job 5 | Failure callback; infrastructure admin |
| Job 5 | playbooks/05_configure_server_role.yml | Requester validation | Failure callback; infrastructure admin |

Use playbooks/90_handle_failure.yml as the starting point for each failure branch.
Obtain the failed node and workflow context from Controller in the eventual adapter.
Entitlement remediation and technical-request corrections restart at Job 1 through
ServiceNow. Later retries require target reconciliation and explicit safe restart points.
Never automatically delete partially provisioned resources as a generic rollback.

## Data between jobs

Pass the approved `request` at workflow launch. Job 3 must eventually publish a
`provisioned_instances` list containing resource ID, provider, hostname, IP and MAC.
Use `ansible.builtin.set_stats` with `per_host: false` for nonsecret downstream artifacts.
Each AAP job is isolated: build inventory in every job needing host connections, or
use an inventory update node. Local facts and add_host alone do not bridge jobs.
The skeleton publishes only a `provisioning_preview` artifact and never invents real IDs.

## Decisions before implementation

Confirm the actual Purview product/API and whether Infoblox owns DNS/IPAM; confirm
cloud provider, VMware scope, Satellite provisioning method, subscription policy,
IdM domain, Certificate Server profile/approval flow, ServiceNow table/state mapping,
network paths, service credentials, role allowlist, and per-server partial failure policy.
Treat IdM host certificates and Certificate Server application certificates separately.
RHEL subscription policy checks should account for the organization's access model;
Simple Content Access must not be treated as a pool-consumption counter.

References:
- https://docs.ansible.com/projects/ansible/latest/collections/ansible/builtin/set_stats_module.html
- https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.6/pdf/using_automation_execution/Red_Hat_Ansible_Automation_Platform-2.6-Using_automation_execution-en-US.pdf
