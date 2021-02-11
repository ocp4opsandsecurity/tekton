# OpenShift Pipelines

OpenShift Pipelines is a Kubernetes-style CI/CD solution based on Tekton. OpenShift Pipelines is designed to run each 
step of the CI/CD pipeline in its own container providing scalable cloud-native CI/CD pipelines. By leveraging in 
OpenShift, pipelines are an automated process that drives software through a path of building, testing, and deploying 
code.

In this article we will create a full-fledged self-serving CI/CD pipeline performing the following tasks:
- Create custom tasks.
- Create a delivery pipeline.
- Provide a storage volume
  - Create a persistent volume claim 
  - Specify a persistent volume claim
- Create a PipelineRun 

Add triggers to capture events in the source repository.

This section uses the pipelines-tutorial example to demonstrate the preceding tasks. The example uses a simple application which consists of:

A front-end interface, vote-ui, with the source code in the ui-repo Git repository.

A back-end interface, vote-api, with the source code in the api-repo Git repository.

The apply-manifests and update-deployment tasks in the pipelines-tutorial Git repository.

## Table Of Contents
- [Assumptions](#assumptions)
- [Components](#components)
- [Features](#features)  
- [Installation](#installation)
- [Concepts](#concepts)  
- [References](#references)

## Assumptions
- Access to the `oc command`
- Access to a user with cluster-admin permissions
- Access to an installed OpenShift Container Platform 4.6 deployment
- Access to an active OpenShift Container Platform 4.6 subscription
- Enable auto-completion using the following command:

## Components
- Tekton Pipelines: v0.16.3
- Tekton Triggers: v0.8.1
- ClusterTasks based on Tekton Catalog 0.16

## Features
- Standardize CI/CD pipelines definitions
- Build images with Kubernetes tools such as S2I, Buildah, Buildpacks, Kaniko
- Deploy applications to multiple platforms such as serverless and VMs
- OpenShift Pipelines are portable across any Kubernetes platforms

## Installation
Install the OpenShift Red Hat OpenShift Pipelines operator using the following command: 
```bash
oc apply -f subscription.yaml
```

Red Hat OpenShift Pipelines Operator adds and configures a ServiceAccount named pipeline that has sufficient 
permissions to build and push an image. This ServiceAccount is used by PipelineRun.
```bash
oc get serviceaccount pipeline
```

## Concepts
### Tasks
Tasks are the building blocks and consist of executed Steps. Steps are commands that achieve a specific goal. Every 
Task runs as a pod and each Step runs in its own container within the same pod.

Apply the **Task** using the following command:
```bash
oc apply -f hello-world/task.yaml
```

List the tasks created above using the [Tekton CLI](https://github.com/tektoncd/cli/releases):
```bash
tkn task ls
```

### TaskRun
A TaskRun executes the Steps in a Task in the sequentially, until all Steps execute successfully, or a failure occurs.

Apply the **TaskRun** using the following command:
```bash
oc apply -f hello-word/task-run.yaml
```

### Pipeline
A Pipeline is a collection of Tasks arranged in a specific order of execution. The pipeline definition optionally 
includes Conditions, Workspaces, Parameters, or Resources depending on the application requirements.

Apply the **Pipeline** using the following command:
```bash
oc apply -f hello-world/pipeline.yaml
```

### PipelineRun
A PipelineRun instantiates a Pipeline for execution with specific inputs, outputs, and execution parameters on a 
cluster.

Apply the **PipelineRun** using the following command:
```bash
oc apply -f hello-world/pipeline-run.yaml
```

### Workspaces
Workspaces declare shared storage volumes that a Task in a Pipeline needs at runtime. Instead of specifying the actual 
location of the volumes, Workspaces enable you to declare the filesystem or parts of the filesystem that would be 
required at runtime. 

- Store Task inputs and outputs
- Share data among Tasks
- Use it as a mount point for credentials held in Secrets
- Use it as a mount point for configurations held in ConfigMaps
- Use it as a mount point for common tools shared by an organization
- Create a cache of build artifacts that speed up jobs

### Triggers
Triggers capture the external events and process them to extract key pieces of information.

#### TriggerBinding
The TriggerBinding resource validates events, extracts the fields from an event payload, and stores them as parameters.

```bash
oc apply -f trigger-binding.yaml
```

#### TriggerTemplate
The TriggerTemplate resource acts as a standard for the way resources must be created. 

```bash
oc apply -f trigger-template.yaml
```

#### EventListener
The EventListener resource provides an endpoint that listens for incoming HTTP-based events with a JSON payload.

```bash
oc apply -f event-listener.yaml
```

#### Trigger
The Trigger resource connects the TriggerBinding and TriggerTemplate resources, and this Trigger resource is referenced 
in the EventListener specification.

## References
- [Adding Operators](https://docs.openshift.com/container-platform/4.6/operators/admin/olm-adding-operators-to-cluster.html#olm-adding-operators-to-a-cluster)
- GitHub
  - [OpenShift Pipelines](https://github.com/openshift/pipelines-tutorial/)
  - [Tekton](https://github.com/tektoncd/pipeline)
- [Understanding Openshift Pipelines](https://docs.openshift.com/container-platform/4.6/pipelines/understanding-openshift-pipelines.html?extIdCarryOver=true&sc_cid=701f2000001OH7iAAG)

