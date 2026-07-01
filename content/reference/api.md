<!-- markdownlint-disable -->
# API Reference

## Packages
- [templates.stakater.com/v1alpha1](#templatesstakatercomv1alpha1)
- [tenantoperator.stakater.com/v1alpha1](#tenantoperatorstakatercomv1alpha1)


## templates.stakater.com/v1alpha1

Package v1alpha1 contains API Schema definitions for the templates.stakater.com v1alpha1 API group

### Resource Types
- [ClusterTemplateInstance](#clustertemplateinstance)
- [Template](#template)
- [TemplateInstance](#templateinstance)


#### ClusterTemplateInstance



ClusterTemplateInstance is the Schema for the clustertemplateinstances API





| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `apiVersion` _string_ | `templates.stakater.com/v1alpha1` | | |
| `kind` _string_ | `ClusterTemplateInstance` | | |
| `metadata` _[ObjectMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.30/#objectmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |  |  |
| `spec` _[ClusterTemplateInstanceSpec](#clustertemplateinstancespec)_ |  |  |  |
| `status` _[ClusterTemplateInstanceStatus](#clustertemplateinstancestatus)_ |  |  |  |


#### ClusterTemplateInstanceSpec



ClusterTemplateInstanceSpec defines the desired state of ClusterTemplateInstance



_Appears in:_
- [ClusterTemplateInstance](#clustertemplateinstance)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `template` _string_ | Template is used to tell what to deploy in matched namespaces |  |  |
| `selector` _[LabelSelector](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.30/#labelselector-v1-meta)_ | Selector is used to filter namespaces where template needs to be deployed |  |  |
| `sync` _boolean_ | Sync is used to keep deployed instance and template in sync |  |  |
| `parameters` _[TemplateInstanceParameter](#templateinstanceparameter) array_ | Parameters hold the values of the defined parameters in the template |  | Optional: \{\} <br /> |


#### TemplateInstanceParameter







_Appears in:_
- [ClusterTemplateInstanceSpec](#clustertemplateinstancespec)
- [TemplateGroupInstanceSpec](#templategroupinstancespec)
- [TemplateInstanceSpec](#templateinstancespec)
- [TemplateInstanceSpec](#templateinstancespec)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `name` _string_ | Name is the name of the parameter to set |  |  |
| `value` _string_ | Value is the value of the parameter to set |  |  |


#### ClusterTemplateInstanceStatus



ClusterTemplateInstanceStatus defines the observed state of ClusterTemplateInstance



_Appears in:_
- [ClusterTemplateInstance](#clustertemplateinstance)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `conditions` _[Condition](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.30/#condition-v1-meta) array_ | Status conditions |  | Optional: \{\} <br /> |
| `deployedNamespaces` _object (keys:string, values:[DeployedNamespaceState](#deployednamespacestate))_ | DeployedNamespaces is a list of namespaces where template has been deployed along with its state. |  | Optional: \{\} <br /> |
| `mappedSecrets` _object (keys:string, values:map[string]MappedResourcesState)_ | MappedSecrets is a list of secrets which have been mapped along with its state. |  | Optional: \{\} <br /> |
| `mappedConfigmaps` _object (keys:string, values:map[string]MappedResourcesState)_ | MappedConfigmaps is a list of configmaps which have been mapped along with its state. |  | Optional: \{\} <br /> |
| `templateManifestsHash` _string_ | TemplateManifestsHash is used to ignore false-positive template.manifests update events |  | Optional: \{\} <br /> |
| `templateResourceMappingHash` _string_ | TemplateResourceMappingHash is used to ignore false-positive template.resourceMappings update events |  | Optional: \{\} <br /> |
| `namespaceCount` _integer_ | NamespaceCount tells the number of namespaces CTI matches |  | Optional: \{\} <br /> |


#### Template



Template is the Schema for the templates API





| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `apiVersion` _string_ | `templates.stakater.com/v1alpha1` | | |
| `kind` _string_ | `Template` | | |
| `metadata` _[ObjectMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.30/#objectmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |  |  |
| `resources` _[TemplateResources](#templateresources)_ |  |  | Optional: \{\} <br /> |
| `spec` _[TemplateSpec](#templatespec)_ |  |  |  |
| `status` _[TemplateStatus](#templatestatus)_ |  |  | Optional: \{\} <br /> |
| `parameters` _[TemplateParameter](#templateparameter) array_ | Parameters can be used to replace certain parts of the template. A parameter is referenced<br />by this format: $\{NAME\}, to parse the value as an expression write $\{\{NAME\}\} instead. Besides the<br />parameters defined here, the following predefined parameters can be used:<br />- $\{NAMESPACE\}: the namespace where the template instance was created<br />- $\{TENANT\}: the tenant name of the tenant that owns the namespace (if any) |  | Optional: \{\} <br /> |


#### TemplateResources



TemplateResources defines a templates resources



_Appears in:_
- [Template](#template)
- [Template](#template)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `manifests` _[EmbeddedResource](#embeddedresource) array_ | manifest represents kubernetes resources that will be deployed into the target namespace |  | Optional: \{\} <br /> |
| `helm` _[HelmConfiguration](#helmconfiguration)_ | helm defines the configuration for a helm deployment |  | Optional: \{\} <br /> |
| `resourceMappings` _[ResourceMapping](#resourcemapping)_ | ResourceMappings defines the secrets/configmaps which will be mapped into the target namespaces |  | Optional: \{\} <br /> |
| `gotemplate` _string_ | gotemplate is a Go template with Sprig functions support which will be rendered to generate Kubernetes resources. |  | Optional: \{\} <br /> |


#### EmbeddedResource



EmbeddedResource holds a kubernetes resource



_Appears in:_
- [TemplateResources](#templateresources)



#### HelmConfiguration



HelmConfiguration holds the helm configuration



_Appears in:_
- [TemplateResources](#templateresources)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `releaseName` _string_ | The helm release name. If omitted the template name will be used |  | Optional: \{\} <br /> |
| `setValues` _[HelmSetValue](#helmsetvalue) array_ | Values in the form of name=value that will be passed to the helm command during<br />helm template |  | Optional: \{\} <br /> |
| `values` _string_ | The additional helm values to use. Expected block string |  | Optional: \{\} <br /> |
| `chart` _[HelmChart](#helmchart)_ | Tells us where to find the helm chart to deploy |  |  |


#### HelmSetValue



HelmSetValue defines a name=value pair that will be passed to helm template



_Appears in:_
- [HelmConfiguration](#helmconfiguration)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `name` _string_ | The path of the value to set |  |  |
| `value` _string_ | The value to set |  |  |
| `forceString` _boolean_ | ForceString specifies if the parameter `--set` or `--set-string` should be used |  | Optional: \{\} <br /> |


#### HelmChart



HelmChart holds the information needed to find a chart to deploy



_Appears in:_
- [HelmConfiguration](#helmconfiguration)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `repository` _[HelmChartRepository](#helmchartrepository)_ | Load helm chart from a repository |  | Optional: \{\} <br /> |


#### HelmChartRepository



HelmChartRepository defines a helm repository where TO can load a chart from



_Appears in:_
- [HelmChart](#helmchart)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `name` _string_ | Name of the chart to deploy |  |  |
| `version` _string_ | Version is the version of the chart to deploy |  | Optional: \{\} <br /> |
| `repoUrl` _string_ | The repo url to use |  | Optional: \{\} <br /> |
| `username` _[HelmSecretRef](#helmsecretref)_ | The username to use for the selected repository |  | Optional: \{\} <br /> |
| `password` _[HelmSecretRef](#helmsecretref)_ | The password to use for the selected repository |  | Optional: \{\} <br /> |


#### HelmSecretRef



HelmSecretRef holds a secret reference to a secret



_Appears in:_
- [HelmChartRepository](#helmchartrepository)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `key` _string_ |  |  |  |
| `name` _string_ |  |  |  |
| `namespace` _string_ |  |  | Optional: \{\} <br /> |


#### ResourceMapping







_Appears in:_
- [TemplateResources](#templateresources)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `secrets` _[Resource](#resource) array_ | Secrets consist of secrets which will be mapped to matching namespaces |  | Optional: \{\} <br /> |
| `configMaps` _[Resource](#resource) array_ | ConfigMaps consist of configMaps which will be mapped to matching namespaces |  | Optional: \{\} <br /> |


#### Resource







_Appears in:_
- [ResourceMapping](#resourcemapping)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `name` _string_ | Name is the name of the resource |  | Required: \{\} <br /> |
| `namespace` _string_ | Namespace is the namespace where the resource lives |  | Required: \{\} <br /> |


#### TemplateSpec







_Appears in:_
- [Template](#template)



#### TemplateStatus







_Appears in:_
- [Template](#template)



#### TemplateParameter







_Appears in:_
- [Template](#template)
- [Template](#template)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `name` _string_ | Name is the name of the parameter |  |  |
| `value` _string_ | Value is the default value of the parameter |  | Optional: \{\} <br /> |
| `required` _boolean_ | If required is true, the template instance must<br />define this parameter, otherwise the deployment will fail. |  | Optional: \{\} <br /> |
| `validation` _string_ | Validation takes a regular expression as value to<br />verify the provided value does match expected values. |  | Optional: \{\} <br /> |


#### TemplateInstance



TemplateInstance is the Schema for the templatesinstance API





| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `apiVersion` _string_ | `templates.stakater.com/v1alpha1` | | |
| `kind` _string_ | `TemplateInstance` | | |
| `metadata` _[ObjectMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.30/#objectmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |  |  |
| `spec` _[TemplateInstanceSpec](#templateinstancespec)_ |  |  |  |
| `status` _[TemplateInstanceStatus](#templateinstancestatus)_ |  |  | Optional: \{\} <br /> |


#### TemplateInstanceSpec



TemplateInstanceSpec holds the expected cluster status of the template instance



_Appears in:_
- [TemplateInstance](#templateinstance)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `template` _string_ | The template to instantiate. This is an immutable field |  |  |
| `sync` _boolean_ | If true the template instance will keep the deployed resources in sync with the template. |  | Optional: \{\} <br /> |
| `parameters` _[TemplateInstanceParameter](#templateinstanceparameter) array_ | Parameters hold the values of the defined parameters in the template |  | Optional: \{\} <br /> |


#### TemplateInstanceStatus



TemplateInstanceStatus describes the current status of the template instance in the cluster



_Appears in:_
- [TemplateInstance](#templateinstance)
- [TemplateInstance](#templateinstance)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `status` _[InstanceDeploymentStatus](#instancedeploymentstatus)_ | Status holds the template instances status |  |  |
| `message` _string_ | A human readable message indicating details about why the namespace is in this condition. |  | Optional: \{\} <br /> |
| `reason` _string_ | A brief CamelCase message indicating details about why the namespace is in this state. |  | Optional: \{\} <br /> |
| `templateHash` _string_ | TemplateHash is used to ignore false-positive template update events |  | Optional: \{\} <br /> |
| `templateManifests` _string_ | TemplateManifests are the manifests that were rendered before |  | Optional: \{\} <br /> |
| `mappedSecrets` _object (keys:string, values:[MappedResourcesState](#mappedresourcesstate))_ | MappedSecrets is a list of secrets which have been mapped along with its state. |  | Optional: \{\} <br /> |
| `mappedConfigmaps` _object (keys:string, values:[MappedResourcesState](#mappedresourcesstate))_ | MappedConfigmaps is a list of configmaps which have been mapped along with its state. |  | Optional: \{\} <br /> |
| `observedAt` _[Time](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.30/#time-v1-meta)_ | LastAppliedAt indicates when the template was last applied |  | Optional: \{\} <br /> |


#### InstanceDeploymentStatus

_Underlying type:_ _string_

InstanceDeploymentStatus describes the status of template instance deployment as {"Deployed", "Failed", ""}



_Appears in:_
- [DeployedNamespaceState](#deployednamespacestate)
- [DeployedNamespaceState](#deployednamespacestate)
- [MappedResourcesState](#mappedresourcesstate)
- [MappedResourcesState](#mappedresourcesstate)
- [TemplateInstanceStatus](#templateinstancestatus)

| Field | Description |
| --- | --- |
| `Deployed` | InstanceDeploymentStatusDeployed describes a succeeded instance deployment<br /> |
| `Failed` | InstanceDeploymentStatusFailed describes a failed instance deployment<br /> |
| `` | InstanceDeploymentStatusPending describes a not yet deployed instance<br /> |


#### DeployedNamespaceState







_Appears in:_
- [ClusterTemplateInstanceStatus](#clustertemplateinstancestatus)
- [TemplateGroupInstanceStatus](#templategroupinstancestatus)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `templateManifests` _string_ | TemplateManifests are the manifests that were rendered before |  | Optional: \{\} <br /> |
| `status` _[InstanceDeploymentStatus](#instancedeploymentstatus)_ |  |  |  |


#### MappedResourcesState







_Appears in:_
- [ClusterTemplateInstanceStatus](#clustertemplateinstancestatus)
- [TemplateGroupInstanceStatus](#templategroupinstancestatus)
- [TemplateInstanceStatus](#templateinstancestatus)
- [TemplateInstanceStatus](#templateinstancestatus)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `reason` _string_ | Reason of resource mapping if failed |  | Optional: \{\} <br /> |
| `status` _[InstanceDeploymentStatus](#instancedeploymentstatus)_ |  |  |  |


## tenantoperator.stakater.com/v1alpha1

Package v1alpha1 contains API Schema definitions for the tenantoperator v1alpha1 API group

### Resource Types
- [Template](#template)
- [TemplateGroupInstance](#templategroupinstance)
- [TemplateGroupInstanceList](#templategroupinstancelist)
- [TemplateInstance](#templateinstance)


#### Template



Template is the Schema for the templates API





| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `apiVersion` _string_ | `tenantoperator.stakater.com/v1alpha1` | | |
| `kind` _string_ | `Template` | | |
| `metadata` _[ObjectMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.30/#objectmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |  |  |
| `resources` _[TemplateResources](#templateresources)_ |  |  | Optional: \{\} <br /> |
| `spec` _[TemplateSpec](#templatespec)_ |  |  |  |
| `status` _[TemplateStatus](#templatestatus)_ |  |  | Optional: \{\} <br /> |
| `parameters` _[TemplateParameter](#templateparameter) array_ | Parameters can be used to replace certain parts of the template. A parameter is referenced<br />by this format: $\{NAME\}, to parse the value as an expression write $\{\{NAME\}\} instead. Besides the<br />parameters defined here, the following predefined parameters can be used:<br />- $\{NAMESPACE\}: the namespace where the template instance was created<br />- $\{TENANT\}: the tenant name of the tenant that owns the namespace (if any) |  | Optional: \{\} <br /> |


#### TemplateSpec







_Appears in:_
- [Template](#template)



#### TemplateStatus







_Appears in:_
- [Template](#template)



#### TemplateGroupInstance



TemplateGroupInstance is the Schema for the TemplateGroupInstances API



_Appears in:_
- [TemplateGroupInstanceList](#templategroupinstancelist)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `apiVersion` _string_ | `tenantoperator.stakater.com/v1alpha1` | | |
| `kind` _string_ | `TemplateGroupInstance` | | |
| `metadata` _[ObjectMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.30/#objectmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |  |  |
| `spec` _[TemplateGroupInstanceSpec](#templategroupinstancespec)_ |  |  |  |
| `status` _[TemplateGroupInstanceStatus](#templategroupinstancestatus)_ |  |  |  |


#### TemplateGroupInstanceSpec



TemplateGroupInstanceSpec defines the desired state of TemplateGroupInstance



_Appears in:_
- [TemplateGroupInstance](#templategroupinstance)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `template` _string_ | Template is used to tell what to deploy in matched namespaces |  |  |
| `selector` _[LabelSelector](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.30/#labelselector-v1-meta)_ | Selector is used to filter namespaces where template needs to be deployed |  |  |
| `sync` _boolean_ | Sync is used to keep deployed instance and template in sync |  |  |
| `parameters` _[TemplateInstanceParameter](#templateinstanceparameter) array_ | Parameters hold the values of the defined parameters in the template |  | Optional: \{\} <br /> |


#### TemplateGroupInstanceStatus



TemplateGroupInstanceStatus defines the observed state of TemplateGroupInstance



_Appears in:_
- [TemplateGroupInstance](#templategroupinstance)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `conditions` _[Condition](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.30/#condition-v1-meta) array_ | Status conditions |  | Optional: \{\} <br /> |
| `deployedNamespaces` _object (keys:string, values:[DeployedNamespaceState](#deployednamespacestate))_ | DeployedNamespaces is a list of namespaces where template has been deployed along with its state. |  | Optional: \{\} <br /> |
| `mappedSecrets` _object (keys:string, values:map[string]MappedResourcesState)_ | MappedSecrets is a list of secrets which have been mapped along with its state. |  | Optional: \{\} <br /> |
| `mappedConfigmaps` _object (keys:string, values:map[string]MappedResourcesState)_ | MappedConfigmaps is a list of configmaps which have been mapped along with its state. |  | Optional: \{\} <br /> |
| `templateManifestsHash` _string_ | TemplateManifestsHash is used to ignore false-positive template.manifests update events |  | Optional: \{\} <br /> |
| `templateResourceMappingHash` _string_ | TemplateResourceMappingHash is used to ignore false-positive template.resourceMappings update events |  | Optional: \{\} <br /> |
| `namespaceCount` _integer_ | NamespaceCount tells the number of namespaces CTI matches |  | Optional: \{\} <br /> |


#### TemplateGroupInstanceList



TemplateGroupInstanceList contains a list of TemplateGroupInstance





| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `apiVersion` _string_ | `tenantoperator.stakater.com/v1alpha1` | | |
| `kind` _string_ | `TemplateGroupInstanceList` | | |
| `metadata` _[ListMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.30/#listmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |  |  |
| `items` _[TemplateGroupInstance](#templategroupinstance) array_ |  |  |  |


#### TemplateInstance



TemplateInstance is the Schema for the templatesinstance API





| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `apiVersion` _string_ | `tenantoperator.stakater.com/v1alpha1` | | |
| `kind` _string_ | `TemplateInstance` | | |
| `metadata` _[ObjectMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.30/#objectmeta-v1-meta)_ | Refer to Kubernetes API documentation for fields of `metadata`. |  |  |
| `spec` _[TemplateInstanceSpec](#templateinstancespec)_ |  |  |  |
| `status` _[TemplateInstanceStatus](#templateinstancestatus)_ |  |  | Optional: \{\} <br /> |


#### TemplateInstanceSpec



TemplateInstanceSpec holds the expected cluster status of the template instance



_Appears in:_
- [TemplateInstance](#templateinstance)

| Field | Description | Default | Validation |
| --- | --- | --- | --- |
| `template` _string_ | The template to instantiate. This is an immutable field |  |  |
| `sync` _boolean_ | If true the template instance will keep the deployed resources in sync with the template. |  | Optional: \{\} <br /> |
| `parameters` _[TemplateInstanceParameter](#templateinstanceparameter) array_ | Parameters hold the values of the defined parameters in the template |  | Optional: \{\} <br /> |




#### InstanceDeploymentStatus

_Underlying type:_ _string_

InstanceDeploymentStatus describes the status of template instance deployment as {"Deployed", "Failed", ""}



_Appears in:_
- [TemplateInstanceStatus](#templateinstancestatus)

| Field | Description |
| --- | --- |
| `Deployed` | InstanceDeploymentStatusDeployed describes a succeeded instance deployment<br /> |
| `Failed` | InstanceDeploymentStatusFailed describes a failed instance deployment<br /> |
| `` | InstanceDeploymentStatusPending describes a not yet deployed instance<br /> |





