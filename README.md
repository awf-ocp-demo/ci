# AWF OCP CI Demo

Demo to run Autoware builds within Kubernetes/Openshift using Tekton.

## Links and References

* [Tekton Documentation](https://tekton.dev/docs/)
* [Kubernetes Documentation](https://kubernetes.io/docs/home/)

## Project Structure

* `images/`: folder that contains files to build linux container images used in this repository;
* `lib/`: folder with several Kubernetes/Openshift resources
* `lib/tekton/`: tekton workloads/resources definitions

## Usage

### Installing Tekton

This section assumes you ate using a remote Kubernetes cluster or 
a local development environment such as minikube or kind; you can skip this
section if using Openshift.

Check the [upstream documentation](https://tekton.dev/docs/installation/) for further details.

#### Tekton Pipelines

Apply the latest stable release:

```bash
kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
```

Check if everything is in place by running:

```bash
kubectl get pods --namespace tekton-pipelines -w
```

#### Tekton Triggers

Apply the latest stable release:

```bash
$ kubectl apply --filename \
https://storage.googleapis.com/tekton-releases/triggers/latest/release.yaml
$ kubectl apply --filename \
https://storage.googleapis.com/tekton-releases/triggers/latest/interceptors.yaml
```

Check if everything is in place by running:

```bash
kubectl get pods --namespace tekton-pipelines --watch
```

#### Tekton CLI:

Install the `tkn` CLI by running (requires a RPM based Linux distribution):

```bash
dnf install -y https://github.com/tektoncd/cli/releases/download/v0.18.0/tektoncd-cli-0.18.0_Linux-64bit.rpm
```

#### Tekton Workloads

Deploy tasks and pipelines from this repository by running:

```bash
for d in "tasks" "pipelines"; do kubectl apply -f lib/tekton/$d; done
```

You those resources with  `kubectl` or `tkn` (the later being more user friendly):

```
$ kubectl get tasks
NAME                    AGE
container-image-build   5m10s
scm-clone               5m10s
$ kubectl describe tasks/scm-clone
Name:         scm-clone
Namespace:    default
Labels:       category=scm
Annotations:  <none>
API Version:  tekton.dev/v1
Kind:         Task
...
```

```
$ tkn tasks list
NAME                    DESCRIPTION   AGE
container-image-build                 6 minutes ago
scm-clone                             6 minutes ago
$ tkn tasks describe scm-clone
Name:        scm-clone
Namespace:   default

Params

 NAME             TYPE     DESCRIPTION              DEFAULT VALUE
 ∙ SCM_URL        string   git url to pull fro...   ---
 ∙ SCM_REFS       string   git refs to use, su...   main
 ∙ RUNNER_IMAGE   string   full container imag...   quay.io/lrossett/tekton-runner:latest

Workspaces

 NAME    DESCRIPTION   OPTIONAL
 ∙ src                 false

Steps

 ∙ fetch
```

You can run a  pipeline creating a `PipelineRun` object:

```
$ kubectl create -f lib/tekton/pipelineruns/ros2-cs9.yml
```

This will run a pipeline to build a ROS2 container using a CentOS Stream9 as its base image.

You can check your pipeline by using the `tkn` CLI:

```
$ tkn pipelinerun list container-build
$ tkn taskrun log $task_name -f
```

## License

[MIT](./LICENSE)
