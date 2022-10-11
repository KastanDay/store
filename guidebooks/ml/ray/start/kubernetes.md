---
imports:
    - kubernetes/kubectl
    - ./resources
    - kubernetes/choose/ns
---

# Kas here.. updating??? -- Install Ray on a Kubernetes Cluster

This will install Ray on a Kubernetes context of your choosing.

The name of the Ray Kubernetes Service:

```shell
export RAY_KUBE_CLUSTER_NAME=${RAY_KUBE_CLUSTER_NAME-mycluster}
```

## Stream out Events from the Ray Head Node

```shell.async
kubectl get events ${KUBE_CONTEXT_ARG} ${KUBE_NS_ARG} --watch-only | tee "${STREAMCONSUMER_EVENTS-/tmp/}kubernetes.txt"
```

```shell.async
if [ -n "$DEBUG_KUBERNETES" ]; then
    echo "Streaming out Ray Pod Logs"
    kubectl logs ${KUBE_CONTEXT_ARG} ${KUBE_NS_ARG} -l ray-cluster-name=${RAY_KUBE_CLUSTER_NAME} -f
fi
```

### Use Helm to create the Ray Cluster

This defines the base docker image we will use for the ray head and worker nodes.

=== "Select Docker image for Ray Operator [default: "rayproject/ray:1.13.0-py37"]"
    ```shell
    export RAY_OPERATOR_IMAGE=RAY_OPERATOR_IMAGE-${choice}
    ```
<!-- 
```shell
export RAY_OPERATOR_IMAGE=${RAY_OPERATOR_IMAGE-rayproject/ray:1.13.0-py37}
``` -->

=== "Select Docker image for Ray Head & Workers. We recommend using the same image. [default: "rayproject/ray:1.13.0-py37"]"
    ```shell
    export RAY_IMAGE=RAY_IMAGE-${choice}
    ```

<!-- ```shell
export RAY_IMAGE=${RAY_IMAGE-$([ $NUM_GPUS = 0 ] && echo rayproject/ray:1.13.0-py37 || echo rayproject/ray-ml:1.13.0-py37-gpu)}
``` -->

A staging directory for the clone of the Ray Helm chart.

```shell
export HELM_CLONE_TEMPDIR=$(mktemp -d)
```

=== "The name of the Ray Kubernetes Service that best describes your ray project: [default: "ray-cluster"]"
    ```shell
    export RAY_KUBE_CLUSTER_NAME=${choice}
    ```

The name of the Ray Kubernetes Service:
<!-- 
```shell
export RAY_KUBE_CLUSTER_NAME=mycluster
``` -->

--8<-- "./kubernetes/install-via-helm.md"
