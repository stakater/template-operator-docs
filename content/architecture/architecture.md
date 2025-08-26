# Architecture

The Multi-Tenant Operator (MTO) is a comprehensive system designed to manage multi-tenancy in Kubernetes environments. Following is the architecture of the MTO:

![architecture](../images/architecture.png)

MTO consists of multiple controllers and components that work together to provide the functionality of the system. The following is a list of the components that make up the MTO system:

| Name | Type | Description |
|------|------|-------------|
| Tenant Controller | Deployment | The Tenant Controller is responsible for managing the creation, deletion, and updating of tenants in the cluster via [Tenant CRD](../kubernetes-resources/tenant/tenant-overview.md). |
| Namespace Controller | Deployment | The Namespace Controller is responsible for managing the creation, deletion, and updating of namespaces in the cluster. |
| Resource Supervisor Deployment | Deployment | The Resource Supervisor Controller is responsible for managing sleep and hibernation of namespaces in the cluster via [ResourceSupervisor CRD](../kubernetes-resources/resource-supervisor.md). |
| Extensions Controller | Deployment | The Extensions Controller enhances MTO's functionality by allowing integration with external services,Currently supports integration with ArgoCD, enabling you to synchronize your repositories and configure AppProjects directly through MTO. It manages extensions via [Extension CRD](../kubernetes-resources/extensions.md). |
| Template Quota Integration Config Controller | Deployment | The Template Quota Integration Config Controller manages 3 different CRDs in one controller, [Template CRD](../kubernetes-resources/template/template.md), [Quota CRD](../kubernetes-resources/quota.md), and [IntegrationConfig CRD](../kubernetes-resources/integration-config.md). |
| TemplateInstance Controller | Deployment | The TemplateInstance Controller is responsible for managing the creation, deletion, and updating of TemplateInstances in the cluster via [TemplateInstance CRD](../kubernetes-resources/template/template-instance.md). |
| ClusterTemplateInstance Controller | Deployment | The ClusterTemplateInstance Controller is responsible for managing the creation, deletion, and updating of ClusterTemplateInstances in the cluster via [ClusterTemplateInstance CRD](../kubernetes-resources/template/template-group-instance.md). |
| Webhook | Deployment | The Webhook is responsible for managing webhook requests from MTO's resources. |
