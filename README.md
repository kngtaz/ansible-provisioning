# Automated server provisioning — Ansible skeleton

Starter project for the ServiceNow → AAP provisioning proof of concept.
**No infrastructure or external systems are changed.** Each job validates the request
shape and prints its integration checklist. A successful preview is not evidence of
provisioning, entitlement availability, connectivity, or registration.

## Playbooks

| Job | Playbook | Purpose |
|---|---|---|
| 1 | `playbooks/01_validate_request.yml` | Target environment and technical validation |
| 2 | `playbooks/02_check_rhel_entitlements.yml` | RHEL service-owner subscription check |
| 3 | `playbooks/03_provision_instances.yml` | Cloud or on-premises compute creation |
| 4A | `playbooks/04a_post_provision.yml` | Host checks, baseline registration, inventory, facts and CSRs |
| 4B | `playbooks/04b_verify_provisioning.yml` | Registration and configuration verification |
| 5 | `playbooks/05_configure_server_role.yml` | Role configuration, certificates, DNS, health and handoff |
| Failure | `playbooks/90_handle_failure.yml` | ServiceNow failure-routing scaffold |

## Local walkthrough

Run from this project folder with ansible-core installed in your chosen environment:

```sh
ansible-playbook playbooks/site.yml -e @vars/request.example.yml --syntax-check
ansible-playbook playbooks/site.yml -e @vars/request.example.yml
ansible-playbook playbooks/01_validate_request.yml -e @vars/request.example.yml
```

Copy `vars/request.example.yml` to the ignored `vars/request.local.yml` to customize
the request. Use `-e @vars/request.local.yml` for local values. Multiple servers and
mixed OS/role requests are represented in the payload; adapters must apply conditions
per server. The preview intentionally lists all planned branches rather than executing them.

## Implementation structure

- `inventories/poc/`: localhost inventory for orchestration and preview.
- `vars/`: default execution mode and sample ServiceNow payload.
- `playbooks/tasks/`: shared payload validation and preview tasks.
- `roles/`: integration role boundaries to implement.
- `templates/ownership.fact.j2`: nonsecret JSON ownership fact template.
- `collections/requirements.yml`: add approved, pinned provider collections here.
- `docs/aap-workflow.md`: template mapping, success/failure edges and artifact contract.

The `skeleton_mode` default is true. Setting it false deliberately fails before the
placeholder integrations. Replace the placeholders with reviewed adapters and real
verification before removing that guard. Do not simply turn off the guard and label
preview results as success. Do not use `--check` as a substitute for this execution mode.

Use AAP credentials or an approved secret store for authentication. Keep private keys
on their destination hosts or in a secret store; do not pass them in workflow artifacts.
Host key checking remains enabled. Do not disable TLS verification in API adapters.

This project does not install AAP, create Controller templates, call ServiceNow,
provision hosts, or perform live integration tests.
# ansible-provisioning
