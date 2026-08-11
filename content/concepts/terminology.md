# Terminology

Terms used throughout the Template Operator documentation.

## Template

A cluster-scoped resource holding the definitions to distribute. A `Template` describes what to create, not where to create it, and on its own it deploys nothing. Definitions come from one of four sources: raw `manifests`, a `helm` chart, an inline `gotemplate`, or `resourceMappings` that copy existing Secrets and ConfigMaps.

## Instance

A resource that applies a `Template` somewhere. Template Operator has two: `TemplateInstance` for a single namespace, and `ClusterTemplateInstance` for every namespace matching a label selector. Both reference a template by name in `spec.template`.

## Target namespace

The namespace an instance renders into. For a `TemplateInstance` this is its own namespace. For a `ClusterTemplateInstance` there is one target namespace per selector match, and the template is rendered separately for each, so parameters resolved from namespace metadata can differ between them.

## Parameter

A named value substituted into a template at render time, referenced as `${NAME}`. A `Template` declares its parameters and may give each a default, mark it `required`, or constrain it with a `validation` regular expression. Instances supply values for those parameters. An instance cannot introduce a parameter the template does not declare.

## Predefined parameter

A parameter resolved by the operator from the target namespace rather than declared in the template, such as `${NAMESPACE}` or `${TENANT}`. See [Template](templates/template.md#parameters) for the full list.

## Expression parameter

A substitution written `${{NAME}}` instead of `${NAME}`. The replacement is parsed as YAML, so the field receives a number, boolean, list, or map rather than a string. See [Template](templates/template.md#strings-and-expressions).

## Resource mapping

A template that copies existing Secrets and ConfigMaps from a source namespace into target namespaces, instead of defining new resources. The operator watches the sources, so later changes to them reach the copies.

## Sync

The `spec.sync` field on an instance. When false, the instance renders once and is then left alone, even if the template changes. When true, the operator keeps the deployed resources aligned with the template. See [TemplateInstance](templates/template-instance.md#sync).

## TemplateGroupInstance

The predecessor of `ClusterTemplateInstance`, part of the legacy `tenantoperator.stakater.com/v1alpha1` API group that Template Operator still serves for migration. New work should use `ClusterTemplateInstance`.
