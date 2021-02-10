# OpenShift Pipelines

OpenShift Pipelines is a Kubernetes-style CI/CD solution based on Tekton. OpenShift Pipelines is designed to run each 
step of the CI/CD pipeline in its own container providing scalable cloud-native CI/CD pipelines. By leveraging in 
OpenShift, pipelines are an automated process that drives software through a path of building, testing, and deploying 
code.

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

## Concepts

### Tasks
Tasks are the building blocks and consist of executed Steps. Steps are commands that achieve a specific goal. Every 
Task runs as a pod and each Step runs in its own container within the same pod.

Apply the `hello-word` **Task** using the following command:
```bash
oc apply -f hello-world/task.yaml
```

### TaskRun
A TaskRun executes the Steps in a Task in the sequentially, until all Steps execute successfully, or a failure occurs.

Apply the `hello-world` **TaskRun** that executes with the relevant input parameters:
```bash
oc apply -f hello-word/task-run.yaml
```

### Pipeline
A Pipeline is a collection of Tasks arranged in a specific order of execution. The pipeline definition optionally 
includes Conditions, Workspaces, Parameters, or Resources depending on the application requirements.

```bash
oc apply -f hello-world/pipeline.yaml
```

### PipelineRun
A PipelineRun instantiates a Pipeline for execution with specific inputs, outputs, and execution parameters on a 
cluster.

```bash
oc apply -f hello-world/pipeline-run.yaml
```

### Workspaces
Workspaces declare shared storage volumes that a Task in a Pipeline needs at runtime. Instead of specifying the actual 
location of the volumes, Workspaces enable you to declare the filesystem or parts of the filesystem that would be 
required at runtime. 

## References
- [Tekton On GitHub](https://github.com/tektoncd/pipeline)
- [Understanding Openshift Pipelines](https://docs.openshift.com/container-platform/4.6/pipelines/understanding-openshift-pipelines.html?extIdCarryOver=true&sc_cid=701f2000001OH7iAAG)

