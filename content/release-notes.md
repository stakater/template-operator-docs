# Changelog

## v0.1.x

### v0.1.8

_**September 17, 2026**_

#### Features

- `Template` now supports Go template expressions in the template body.
- Helm render failures are now reported on the instance status instead of only in the operator logs.

#### Bug Fixes

- Fixed `ClusterTemplateInstance` not creating informers for some of the resource kinds it deploys, so changes to those resources went unwatched.
- Fixed `ClusterTemplateInstance` status reporting a stale `namespaceCount`.
- Fixed the operator's admin ClusterRoleBinding being created incorrectly when the operator is installed outside the `template-operator-system` namespace.

#### Enhancements

- Bundled Helm upgraded to `v3.21.2`.
- Go base images upgraded to 1.25, along with Kubernetes client library and security updates.

!!! note
    v0.1.6 and v0.1.7 are private releases; all their changes ship in v0.1.8.

### v0.1.5

_**August 29, 2025**_

#### Bug Fixes

- Migration no longer sets owner references on the new CRs, and no longer deletes them.

### v0.1.4

This version acts as a starting version for Template Operator, introducing [Multi Tenant Operator's](https://docs.stakater.com/mto/latest/) resource provisioning support as a separate product.
