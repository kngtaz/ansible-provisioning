# Integration roles

Add real roles here when platform APIs and credential types are confirmed:

- request_validation: compute capacity, approved images, network and inventory checks.
- subscription_policy: service-owner allocation policy and RHEL subscription checks.
- compute_provision: VMware or the selected cloud provider; return immutable resource IDs.
- baseline_configuration: Satellite, IdM, host groups, machine certificates and local facts.
- application_certificates: CSR submission, issuance polling and certificate retrieval.
- role_configuration: allowlisted application role dispatch and health checks.
- inventory_sync: the confirmed Purview/Infoblox product and authoritative fields.
- servicenow_reporting: ticket updates, assignment and closure routing.

Use idempotent modules where available. Add retries only to operations safe to repeat.
Do not place credentials or private keys in role defaults or workflow artifacts.
