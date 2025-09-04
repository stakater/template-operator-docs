---
head:
  - - meta
    - name: keywords
      content: template operator, kubernetes, multi-tenancy, templates
---

# Welcome to the Docs

Managing Kubernetes clusters can be complex, requiring careful orchestration of resources, security, and automation. While Kubernetes provides foundational building blocks, achieving secure, scalable, and efficient multi-tenancy often demands advanced expertise and custom solutions.

**Template Operator** is designed to address these challenges by extending Kubernetes with powerful templating and automation capabilities. It enables administrators to define reusable templates for namespaces and resources, automate their distribution, and streamline the provisioning of tenant environments. With Template Operator, you can:

- **Create and manage reusable templates** for Kubernetes manifests, Helm charts, and more.
- **Parameterize templates** for flexible, tenant-specific deployments.
- **Automate resource distribution** across namespaces using Template Instances and Cluster Template Instances.
- **Synchronize resources** such as secrets and ConfigMaps between namespaces.
- **Adopt GitOps workflows** with native Kubernetes custom resources.
- **Achieve multi-tenancy** without additional management layers.

Template Operator simplifies cluster administration, enhances security, and enables efficient resource utilization, making multi-tenancy accessible and manageable for organizations of all sizes.

## Installation

Refer to the [installation guide](./installation/overview.md) for setting up Template Operator.
