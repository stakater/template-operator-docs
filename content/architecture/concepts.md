# Concepts

Here are the key concepts of Template Operator:

## Template

A **Template** is a reusable blueprint in Template Operator that defines configurations for Kubernetes resources. It supports raw manifests, gotemplate based dynamic manifests, Helm charts, or resource mappings, enabling standardization and automation across multiple tenants.

## Template Instance (TI)

A **Template Instance** is a concrete implementation of a **Template**, created with specific parameters tailored for a particular tenant or use case. It generates actual Kubernetes resources based on the defined template.

## Cluster Template Instance (CTI)

A **Cluster Template Instance** works on a particular set of namespaces based on the mentioned labels, taking **Template** as a reference for the resources to be deployed. It simplifies managing multiple interdependent resources for complex tenant setups.
