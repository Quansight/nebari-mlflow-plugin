# Nebari Plugin MLflow AWS

**Table of Contents**

- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [License](#license)

## Introduction
This MLflow extension currently only supports Azure Nebari deployments, but aims to integrate into Nebari deployments utilizing the Azure, AWS, GCP, and DO providers. It provides a robust, collaborative environment for AI/ML professionals to manage experiments, track metrics, and deploy models.

### Features
**Centralized Artifact Repository**: Store and manage all your metrics, parameters, and artifacts in a single location, accessible across the multi-tenant platform.

**Experiment Tracking**: Log, query, and visualize metrics to understand and compare different runs and models.

**Automated Configuration**: Simply type import mlflow in your Python script, and you're already configured to communicate with the remote multi-tenant MLflow server—no additional setup required.

### Installation
Prerequisites:
- Nebari must be deployed using the provider AWS
- Nebari version 2024.6.1 or later

Installing the MLflow extension is as straightforward as installing a Python package. Run the following commands:

```bash
git clone nebari-plugin-mlflow-aws
cd nebari-plugin-mlflow-aws/
pip install nebari-plugin-mlflow-aws
```
This command installs the Python package and also creates the necessary infrastructure to run MLflow on the AI Platform.

### Configuration
After installation, the MLflow extension is automatically configured to work with the AI Platform. To access the MLflow interface, navigate to <https://[your-nebari-domain]/mlflow>.

For Azure, your app registration will need RBAC permissions in addition to the typical Contributor permissons.  We recommend you create a custom role scoped at the resource_group (usually named <project_name>-<namespace> where the values are what you set in nebari-config.yaml), and add the Microsoft.Authorization/roleAssignments/read, Microsoft.Authorization/roleAssignments/write, Microsoft.Authorization/roleAssignments/delete permissions.  Then create a role assignment of that role to the nebari app registration.

#### Configuring MLFlow Kubernetes Namespace
In order for MLflow to deploy into a non-default Kubernetes namespace, you may add an mlflow block to your Nebari configuration file such as the example below. Although MLflow resources should all be prefixed to avoid collisions, we still recommend this as a best practice.
```yaml
mlflow:
  namespace: mlflow
```

#### Exposing MLflow workload via Ingress
In order for the traefik ingress to route users' web requests to the MLflow workload, add or update the block in your Nebari configuration file. Be sure to update this block if you've configured a namespace other than `mlflow`.
```yaml
ingress:
  terraform_overrides:
    additional-arguments:
    - "--providers.kubernetescrd.namespaces=mlflow"
```

#### Configuring MLflow Tracking URL
You may set the `MLFLOW_TRACKING_URL` to configure mlflow in individual users' Nebari instances by adding or updating an additional block in your Nebari configuration file. Note that 'dev' in the URL below corresponds to the Kubernetes namespace where mlflow is deployed, so if you have assigned a custom namespace, that URL will need updating.
```yaml
jupyterhub:
  overrides:
    singleuser:
      extraEnv:
        MLFLOW_TRACKING_URI: "http://mlflow-svc.dev.svc:5000"
```

### Usage
Getting started with the MLflow extension is incredibly simple. To track an experiment:

Navigate to the MLFLow extension URL and create a new experiment.
In your Python script, import MLflow and start logging metrics.
```python
import mlflow

# Start an experiment
with mlflow.start_run() as run:
    mlflow.log_metric("accuracy", 0.9)
    mlflow.log_artifact("path/to/your/artifact")
```
With the above code, your metrics and artifacts are automatically stored and accessible via the MLFlow extension URL.


## License

`nebari-mlflow-plugin` is distributed under the terms of the [Apache](./LICENSE.md) license.
