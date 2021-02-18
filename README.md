# OpenShift Pipelines

OpenShift Pipelines is a CI/CD solution based on Tekton. By leveraging in 
OpenShift, pipelines are an automated process that drives software through 
a path of building, testing, and deploying code.

In this article we will be performing the following tasks:
- Create custom tasks.
- Create a custom pipeline.
- Create storage volumes.
- Execute PipelineRuns.

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
- Ansible Runner Task: 0.1

The custom resources needed to define a pipeline are listed below:
- `Task`: a reusable, loosely coupled number of `Steps`
- `Pipeline`: the definition of the `Tasks` that it should perform
- `TaskRun`: the execution and result of running an instance of a `Task`
- `PipelineRun`: the execution and result of running an instance of a `Pipeline`
- `Pipeline`: includes a number of `TaskRuns`
  
## Features
- Standardize pipelines definitions
- Build images with Kubernetes tools
- Deploy applications to multiple platforms

## Installation
Export environment variables:
```bash
export NAMESPACE=<your-pipeline-project>
```

Create a new pipeline project:
```bash
oc new-project $NAMESPACE
```

### Apply Subscription
Install the OpenShift Pipelines operator using the following command: 
```bash
oc apply -f openshift/subscription.yaml \
         -l operator=pipelines
```

OpenShift Pipelines Operator adds and configures a ServiceAccount named 
`Pipeline` that has sufficient permissions to build and push an image. This 
ServiceAccount is used by `PipelineRun`.
```bash
oc get serviceaccount pipeline -n $NAMESPACE
```

## Concepts
### Tasks
Tasks are the building blocks and consist of executed Steps. Steps are commands 
that achieve a specific goal. Every Task runs as a pod and each Step runs in 
its own container within the same pod.

#### Tekton Hub
Discover, search and share reusable Tasks and Pipelines.
> https://hub-preview.tekton.dev

### Pipelines
A `Pipeline` is a collection of `Tasks` arranged in a specific order of execution.

### Compliance Pipeline Example
Install the OpenShift Compliance operator using the following command:
```bash
oc apply -f openshift/subscription.yaml \
         -l operator=compliance
```

Apply the `Pipeline` and `Task` using the following command:
```bash
oc apply -n $NAMESPACE \
         -f compliance/pipeline.yaml \
         -f compliance/task.yaml
```

List the `Task`:
```bash
tkn task ls -n $NAMESPACE
```

List the `Pipeline`:
```bash
tkn pipeline ls -n $NAMESPACE
```

Execute the moderate `Pipeline` using the following command:
```bash
tkn pipeline start moderate-compliance-pipeline \
    -w name=shared-workspace,volumeClaimTemplateFile=compliance/pvc.yaml \
    -p namespace=$NAMESPACE \
    --showlog
```

##### Compliance TaskRun
A TaskRun executes the `Steps` in a `Task` in the sequentially, until all 
`Steps` execute successfully, or a failure occurs.

List the `Taskrun` using the following command:
```bash
tkn taskrun ls -n $NAMESPACE
```

### Workspaces
Workspaces declare shared storage volumes that a `Task` in a `Pipeline` needs 
at runtime. Instead of specifying the actual location of the volumes, 
Workspaces enable you to declare the filesystem or parts of the filesystem that 
would be required at runtime. 

- Store Task inputs and outputs
- Share data among Tasks
- Use it as a mount point for credentials held in Secrets
- Use it as a mount point for configurations held in ConfigMaps
- Use it as a mount point for common tools shared by an organization
- Create a cache of build artifacts that speed up jobs

## References
- [Adding Operators](https://docs.openshift.com/container-platform/4.6/operators/admin/olm-adding-operators-to-cluster.html#olm-adding-operators-to-a-cluster)
- CLI
  - [Tekton Tools](https://github.com/tektoncd/cli/releases)
- GitHub
  - [OpenShift Pipelines Repository](https://github.com/openshift/pipelines-tutorial/)
  - [Tekton Repository](https://github.com/tektoncd/pipeline)
- [Understanding Openshift Pipelines](https://docs.openshift.com/container-platform/4.6/pipelines/understanding-openshift-pipelines.html?extIdCarryOver=true&sc_cid=701f2000001OH7iAAG)

