# Helm in Kubernetes -- Multiple Environment Setup

## Repository Structure

    repo/
     ├── helm-chart/
     ├── values-dev.yaml
     ├── values-stage.yaml
     └── values-prod.yaml

## Concept

In Kubernetes deployments, the **same application** often needs to run
in multiple environments such as: - Development (Dev) - Staging
(Stage) - Production (Prod)

Instead of creating separate Helm charts for each environment, we use
**one Helm chart** and **multiple values files**.

Helm reads the configuration from the appropriate values file depending
on the environment.

------------------------------------------------------------------------

## 1. helm-chart Folder

This folder contains the main Helm chart which defines the Kubernetes
resources.

Example structure:

    helm-chart/
     ├── Chart.yaml
     ├── values.yaml
     └── templates/
          ├── deployment.yaml
          └── service.yaml

The templates folder contains Kubernetes resource templates such as
Deployments and Services.

Example template snippet:

    replicas: {{ .Values.replicaCount }}
    image: {{ .Values.image.repository }}:{{ .Values.image.tag }}

These values are dynamically replaced using the values files.

------------------------------------------------------------------------

## 2. values-dev.yaml

Configuration used for the **Development environment**.

Example:

    replicaCount: 1
    image:
      repository: myapp
      tag: dev

Dev environment usually has: - Fewer replicas - Lower resource usage -
Used for testing by developers

------------------------------------------------------------------------

## 3. values-stage.yaml

Configuration used for the **Staging environment**.

Example:

    replicaCount: 2
    image:
      repository: myapp
      tag: stage

Staging environment is used for: - Pre-production testing - QA
validation

------------------------------------------------------------------------

## 4. values-prod.yaml

Configuration used for the **Production environment**.

Example:

    replicaCount: 5
    image:
      repository: myapp
      tag: prod

Production environment typically has: - More replicas - Higher
reliability - Used by real users

------------------------------------------------------------------------

## Deployment Commands

### Deploy to Dev

    helm install myapp ./helm-chart -f values-dev.yaml

### Deploy to Staging

    helm install myapp-stage ./helm-chart -f values-stage.yaml

### Deploy to Production

    helm install myapp-prod ./helm-chart -f values-prod.yaml

------------------------------------------------------------------------

## How Helm Works Internally

1.  Helm reads the templates from the Helm chart.
2.  It loads values from the selected values file.
3.  Variables in templates are replaced with those values.
4.  The final Kubernetes YAML is generated.
5.  Kubernetes creates the resources in the cluster.

------------------------------------------------------------------------

## Advantages

-   One chart for all environments
-   Environment-specific configuration
-   Easy maintenance
-   Works well with CI/CD pipelines
-   Reduces duplication of YAML files

------------------------------------------------------------------------

## Simple Definition

Helm allows us to deploy the same Kubernetes application in multiple
environments by using one chart and different values files for each
environment.
