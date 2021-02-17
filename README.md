# OpenShift Pipelines

OpenShift Pipelines is a Kubernetes-style CI/CD solution based on Tekton. OpenShift Pipelines is designed to run each 
step of the CI/CD pipeline in its own container providing scalable cloud-native CI/CD pipelines. By leveraging in 
OpenShift, pipelines are an automated process that drives software through a path of building, testing, and deploying 
code.

In this article we will be performing the following tasks:
- Create custom tasks.
- Create custom pipelines.
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

## Features
- Standardize CI/CD pipelines definitions
- Build images with Kubernetes tools such as S2I, Buildah, Buildpacks, Kaniko
- Deploy applications to multiple platforms such as serverless and VMs
- OpenShift Pipelines are portable across any Kubernetes platforms

## Installation
Export environment variables:
```bash
export PIPELINE_PROJECT=<your-pipeline-project>
```

Create a pipeline project:
```bash
oc new-project $PIPELINE_PROJECT
```

### Apply Subscription
Install the OpenShift Red Hat OpenShift Pipelines operator using the following command: 
```bash
oc apply -n $PIPELINE_PROJECT \
         -f openshift/pipeline-subscription.yaml
```

Red Hat OpenShift Pipelines Operator adds and configures a ServiceAccount named pipeline that has sufficient 
permissions to build and push an image. This ServiceAccount is used by PipelineRun.
```bash
oc get serviceaccount pipeline -n $PIPELINE_PROJECT
```

## Concepts
### Tasks
Tasks are the building blocks and consist of executed Steps. Steps are commands that achieve a specific goal. Every 
Task runs as a pod and each Step runs in its own container within the same pod.

#### Tekton Hub
Discover, search and share reusable Tasks and Pipelines
> https://hub-preview.tekton.dev

### Pipelines
A Pipeline is a collection of Tasks arranged in a specific order of execution. The pipeline definition optionally
includes Conditions, Workspaces, Parameters, or Resources depending on the application requirements.

### Compliance Pipeline
Apply the **Pipeline** and **task** using the following command:
```bash
oc apply -n $PIPELINE_PROJECT \
         -f compliance/pipeline.yaml \
         -f compliance/task.yaml
```

List the **task**:
```bash
tkn task ls -n $PIPELINE_PROJECT
```

List the **pipline**:
```bash
tkn pipeline ls -n $PIPELINE_PROJECT
```

Execute **pipeline** using the following command:
```bash
tkn pipeline start compliance-pipeline \
    -w name=shared-workspace,volumeClaimTemplateFile=compliance/pvc.yaml \
    -p namespace=pipeline.yaml-tutorial \
    --showlog
```

##### Compliance TaskRun
A TaskRun executes the `Steps` in a `Task` in the sequentially, until all `Steps` 
execute successfully, or a failure occurs.

List the **taskrun** using the following command:
```bash
tkn taskrun ls -n $PIPELINE_PROJECT
```

#### Service Mesh Pipeline
##### Ansible Runner Task
Ansible Runner Task allows running the Ansible Playbooks using the 
ansible-runner tool.
- Parameters
  - project-dir: The ansible-runner private data dir
  - args:: The array of arguments to pass to the runner command (default: --help)
- Workspaces
  - runner-dir: A workspace to hold the private_data_dir as described in https://ansible-runner.readthedocs.io/en/latest/intro.html#runner-input-directory-hierarchy[Runner Directory]

Apply the **ansible-runner** task using the following command:
```bash
oc apply -n $PIPELINE_PROJECT \
         -f https://raw.githubusercontent.com/tektoncd/catalog/master/task/ansible-runner/0.1/ansible-runner.yaml
```

List the project level **task**:
```bash
tkn task ls -n $PIPELINE_PROJECT
```

Add deployer

Deploy Services

Deploy 

```bash
tkn clustertasks ls -n $PIPELINE_PROJECT
```

Apply the **Pipeline** and **Task** using the following command:
```bash
oc apply -n $PIPELINE_PROJECT \
         -f ansible/pipeline.yaml.yaml \
         -f ansibls/task.yaml
```
### Workspaces
Workspaces declare shared storage volumes that a `Task` in a `Pipeline` needs at runtime. Instead of specifying the actual 
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
- CLI
  - [Tekton Tools](https://github.com/tektoncd/cli/releases)
- GitHub
  - [OpenShift Pipelines Repository](https://github.com/openshift/pipelines-tutorial/)
  - [Tekton Repository](https://github.com/tektoncd/pipeline)
- [Understanding Openshift Pipelines](https://docs.openshift.com/container-platform/4.6/pipelines/understanding-openshift-pipelines.html?extIdCarryOver=true&sc_cid=701f2000001OH7iAAG)

