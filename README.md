 # krs—Kubernetes resource stats

<div style="text-align: center; margin-bottom: 50px;">
  <img src="om-k8s.png" width="200px" />
</div>

`krs` is a command line tool for capturing and serializing Kubernetes resource statistics in [OpenMetrics](https://github.com/OpenObservability/OpenMetrics) format. It dumps statistics about Kubernetes resources, for example the number of pods in a certain namespace, on a periodic basis to local storage. The kind of resources (pods, services, etc.) as well as the scope, that is, cluster-level or a list of namespaces, is configurable. You can use `krs` either on the client-side (for example, from your laptop) or in-cluster, like in a deployment. Note that `krs` leaves the decision where and how long-term storage is carried out up to you.

## Install

In order to use `krs` you must meet the following two prerequisites:

1. `kubectl` must be [installed](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
1. Access to a Kubernetes cluster must be configured. 

Here's my test environment: Minikube@v1.10, OpenShift Online@v1.9, AKS@v1.0 with a `kubectl`@v1.11 on macOS.

## From binaries

Binaries for the following platforms are available:

- [Linux](https://github.com/mhausenblas/krs/releases/download/0.1/krs_linux) 
- [macOS](https://github.com/mhausenblas/krs/releases/download/0.1/krs_macos) 
- [Windows](https://github.com/mhausenblas/krs/releases/download/0.1/krs_windows)

To download a binary, for example, on a Linux system, do:

```shell
$ curl -s -L https://github.com/mhausenblas/kubed-sh/releases/download/0.5.1/kubed-sh-linux -o kubed-sh
```

### From source

Assuming you've got Go in version 1.10 or above installed you can install `krs` from source like so:

```shell
$ go get -u github.com/mhausenblas/krs
```

### From Kubernetes

You can launch `krs` like so:

```shell
$ kubectl run krs --image=quay.io/mhausenblas/krs:0.1
```

## Use

`krs` assumes that `kubectl` is installed and configured. It writes the OpenMetrics data to `stdout` which you can then redirect to a file or process further. 

For example, to gathers stats of the `dev42` namespace and store the OpenMetrics formatted  stats in a file called `/tmp/krs/2018-10-05.om` as well as see the errors on screen (via `stdout`), do the following:

```shell
$ krs dev42 >> /tmp/krs/2018-10-05.om
```

If you don't provide a namespace as the first argument, `krs` will watch the `default` namespace. Note that with the environment variable `KRS_KUBECTL_BIN` you can set the `kubectl` to use, which, especially under Windows is required.

The beginning of the output of the [end-to-end test](e2e.sh) looks as follows, with the complete output as seen in [e2e-output.om](e2e-output.om):

```
# HELP pods Number of pods in any state, for example running
# TYPE pods gauge
pods{namespace="krs"} 2
# HELP deployments Number of deployments
# TYPE deployments gauge
deployments{namespace="krs"} 2
# HELP services Number of services
# TYPE services gauge
services{namespace="krs"} 1
# HELP pods Number of pods in any state, for example running
# TYPE pods gauge
pods{namespace="krs"} 2
# HELP deployments Number of deployments
# TYPE deployments gauge
deployments{namespace="krs"} 2
# HELP services Number of services
# TYPE services gauge
services{namespace="krs"} 1
```

