# On OpenShift

Template Operator is [RedHat Certified](https://catalog.redhat.com/software/operators/detail/618fa05e3adfdfc43f73b126) operator available on the Red Hat MarketPlace.

This document contains instructions on installing, uninstalling and configuring Multi Tenant Operator on OpenShift.

1. [OpenShift OperatorHub UI](#installing-via-operatorhub-ui)

1. [CLI/GitOps](#installing-via-cli-or-gitops)

1. [Uninstall](#uninstall-via-operatorhub-ui)

## Requirements

* An **OpenShift** cluster

## Installing via OperatorHub UI

* After opening OpenShift console click on `Operators`, followed by `OperatorHub` from the side menu

  ![OperatorHub](../images/operatorhub.png)

* Now search for `Template Operator` and then click on `Template Operator` tile

  ![alt text](../images/template-operator.png)

* Click on the `install` button

  ![alt text](../images/operator-details.png)

* Keep the `Update Channel` and `Version` dropdowns at the latest versions. Select Operator recommended Namespace in `Installed Namespace` section.

  ![alt text](../images/install-tab.png)

* Wait for the operator to be installed

  ![alt text](../images/successful-install.png)

  > Note: Template Operator will be installed in `template-operator-system` namespace.

## Installing via CLI OR GitOps

* Create namespace `template-operator-system`

  ```bash
  oc create namespace template-operator-system
  namespace/template-operator-system created
  ```

* Create an OperatorGroup YAML for Template Operator and apply it in `template-operator-system` namespace.

  ```bash
  oc create -f - << EOF
  apiVersion: operators.coreos.com/v1
  kind: OperatorGroup
  metadata:
    name: template-operator
    namespace: template-operator-system
  EOF
  operatorgroup.operators.coreos.com/template-operator created
  ```

* Create a subscription YAML for Template Operator and apply it in `template-operator-system` namespace.

  ```bash
  oc create -f - << EOF
  apiVersion: operators.coreos.com/v1alpha1
  kind: Subscription
  metadata:
    name: template-operator
    namespace: template-operator-system
  spec:
    channel: release-0.1
    installPlanApproval: Automatic
    name: template-operator
    source: certified-operators
    sourceNamespace: openshift-marketplace
    startingCSV: template-operator.v0.1.5
  EOF
  subscription.operators.coreos.com/template-operator created
  ```

  > Note: To bring Template Operator via GitOps, add the above files in GitOps repository.

* After creating the `subscription` custom resource, open OpenShift console and click on `Operators`, followed by `Installed Operators` from the side menu and wait for the installation to complete

  ![alt text](../images/wait-for-installation.png)

## Uninstall via OperatorHub UI

You can uninstall Template Operator by following these steps:

* After making the required changes open OpenShift console and click on `Operators`, followed by `Installed Operators` from the side menu

  ![image](../images/wait-for-installation.png)

* Now click on uninstall and confirm uninstall.

  ![alt text](../images/uninstall.png)

* Now the operator has been uninstalled.

  > Note: you can also manually remove Template Operator's CRDs and its resources from the cluster.
