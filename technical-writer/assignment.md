# Debug Kubernetes with kubectl

Kubernetes (k8s) has several commands you can use to debug issues with kubectl, the k8s Command Line Interface (CLI). kubectl communicates with the k8s API layer, and works across all major cloud providers, including Azure. 

## Get Pod Status

Use `kubectl get pods` to retrieve a list of all pods and their status. If you have multiple namespaces set up, specify the namespace when using this command. 

```shell
kubectl get pods --namespace default
```

```shell
NAME                      READY   STATUS    RESTARTS   AGE
NAME                           READY   STATUS         RESTARTS   AGE
demo-cron-29374632-zf6cr       0/1     Completed      0          93s
demo-job-dsq62                 0/1     Completed      0          93s
demo-job-j46v8                 0/1     Error          0          107s
demo-pod                       0/1     ErrImagePull   0          9s
echo-deploy-64bb977ddd-j28sm   1/1     Running        0          107s
echo-deploy-64bb977ddd-v5jc8   1/1     Running        0          107s
```

### Access Pod Logs

Use `kubectl logs` along with the pod name to retrieve the logs of a specific pod. This command helps you review logs and debug container issues.

```shell
kubectl logs my-app-59854d5646-lmtgx 
```

```shell
2025/11/07 00:16:30 [notice] 1#1: using the "epoll" event method
2025/11/07 00:16:30 [notice] 1#1: nginx/1.29.3
2025/11/07 00:16:30 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19) 
2025/11/07 00:16:30 [notice] 1#1: OS: Linux 6.11.11-linuxkit
2025/11/07 00:16:30 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
```

### Execute Commands in Containers

Use `kubectl exec` to execute a command on a container from the inside or to explore the container's environment. This command expects additional inputs, such as the pod name, container info, and other flags. See [kubectyl exec](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_exec/) for more info. For this example, we'll use -it and -- bin/bash, which will <!--add in at computer-->

```shell
kubectl exec -it my-app-59854d5646-lmtgx -- /bin/bash
```

```shell
root@nginx-deployment-test1:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

## Recommended Workflow

We recommend this sequence for debugging:

1. Start with `kubectl get pods` to identify problematic pods.
2. Use `kubectl logs <podname>` to examine pod logs for error messages.
3. Use `kubectl exec` to explore the container environment and review configuration files.

## Advanced Debugging

The `kubectl debug` command provides another option for debugging containers. This command creates a clone of a pod that does not stop if an error occurs inside the container.

```shell
kubectl debug nginx-deployment-abc123 -it --image=busybox
```

## References

- [kubectl Commands](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-strong-getting-started-strong-)
- [What is Kubernetes](https://kubernetes.io/docs/concepts/overview/)
