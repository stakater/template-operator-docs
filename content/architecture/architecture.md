# Architecture

The Template Operator is a comprehensive system designed to manage multi-tenancy in Kubernetes environments. Following is the architecture of the MTO:

Template Operator consists of multiple controllers and components that work together to provide the functionality of the system. The following is a list of the components that make up the Template Operator system:

| Name | Type | Description |
|------|------|-------------|
| Template Operator Manager | Deployment | The Template Operator Manager manages 3 different CRDs in one controller; Template, Template Instance and Cluster Template Instance. |
| Template Operator Old Manager | Deployment | The Template Operator Old Manager manages older/legacy CRDs of the older version of operator, which were part of Multi Tenant Operator. It serves to handle older versions if needed during migration. |
| Webhook | Deployment | The Webhook is responsible for managing webhook requests from Custom Resources. |
