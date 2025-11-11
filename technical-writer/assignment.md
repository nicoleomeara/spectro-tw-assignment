# Debug Kubernetes with `kubectl`

You use `kubectl`, the Kubernetes Command-Line Interface (CLI), to work directly with your cluster. `kubectl` talks to the Kubernetes API server and works with any standard cluster, whether it’s on your local machine or in a Docker container. This guide explains practical ways to debug pods and containers using common `kubectl` commands.

## Check Pod Status

Use `kubectl get pods` to list all pods and their current status. If your cluster has more than one namespace, add the `--namespace <name>` or `-n <name>` flag to list pods in a specific namespace.

**Note**: We use `<name>`, `<podname>`, and `<command>` throughout this guide as placeholders. When you execute different `kubectl` commands, replace the placeholders with your actual values.

```shell
kubectl get pods --namespace sandbox
```

```shell
NAME                           READY   STATUS      RESTARTS   AGE
demo-cron-29378922-f2v58       0/1     Completed   0          5m38s
demo-cron-29378924-hlv9b       0/1     Completed   0          3m38s
demo-cron-29378926-2s897       0/1     Completed   0          98s
demo-job-gd58w                 0/1     Completed   0          11m
echo-deploy-64bb977ddd-g6cgj   1/1     Running     0          11m
echo-deploy-64bb977ddd-tdmg7   1/1     Running     0          11m
logger                         1/1     Running     0          11m
nginx-demo                     1/1     Running     0          2m49s
```

## View Pod Logs

If a pod shows an error in the status column, check its logs with `kubectl logs <podname>`.

```shell
kubectl logs nginx-demo 
```

```shell
2025/11/10 00:44:54 [notice] 1#1: using the "epoll" event method
2025/11/10 00:44:54 [notice] 1#1: nginx/1.29.1
2025/11/10 00:44:54 [notice] 1#1: built by gcc 14.2.0 (Alpine 14.2.0) 
2025/11/10 00:44:54 [notice] 1#1: OS: Linux 6.11.11-linuxkit
2025/11/10 00:44:54 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
```

### Execute Commands in Containers

Use `kubectl exec <podname> -- <command>` to issue shell commands inside a container. For this example,  `kubectl exec nginx-demo -- cat /etc/os-release` displays information about the Operating System (OS) in the container. See the [kubectl exec documentation](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_exec/) for more details about the available commands.

```shell
kubectl exec nginx-demo -- cat /etc/os-release
```

```shell
NAME="Alpine Linux"
ID=alpine
VERSION_ID=3.22.1
PRETTY_NAME="Alpine Linux v3.22"
HOME_URL="https://alpinelinux.org/"
BUG_REPORT_URL="https://gitlab.alpinelinux.org/alpine/aports/-/issues"
```

## Recommended Debug Workflow

This workflow helps you find and fix common issues with pods and containers. Each step builds on the last, so you can identify problems, review logs, and inspect the container environment in a logical order.

1. Use `kubectl get pods` to identify pods with issues.
2. Use `kubectl logs <podname>` to review logs for errors.
3. Use `kubectl exec <podname> -- <command>` to inspect the container environment or review configuration files.

## Advanced: `kubectl debug`

If you still can't find the root issue after trying the steps above, use `kubectl debug` to create a temporary copy of a pod for troubleshooting. The cloned pod stays active even if an error occurs in the container. In the example below, `nginx-demo` is the name of the original pod. The `-it` flag starts an interactive terminal session, and `--image=busybox:1.36.1` sets the container image for the debug pod.

```shell
kubectl debug nginx-demo -it --image=busybox:1.36.1 --profile=general
```

```shell
Defaulting debug container name to debugger-t884j.
All commands and output from this session will be recorded in container logs, including credentials and sensitive information passed through the command prompt.
If you don't see a command prompt, try pressing enter.
/ # 
/ # 

```

Use the interactive shell in the cloned pod to check for problems and fix issues without changing the original pod.

## References

- [`kubectl` Commands](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)
- [Kubernetes Overview](https://kubernetes.io/docs/concepts/overview/)
