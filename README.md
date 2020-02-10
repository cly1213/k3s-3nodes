# k3s
Reference: http://devnetstack.com/kubernetes-the-easy-way-with-k3s/
## Three nodes version
m1, n1, n2
<br />

## Enter to Master Node
### Verification
```sh
vagrant ssh m1
```
On master node
```sh
vagrant@m1:~$ kubectl get no -o wide
NAME   STATUS   ROLES    AGE   VERSION        INTERNAL-IP     EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
m1     Ready    master   57m   v1.17.2+k3s1   192.168.0.200   <none>        Ubuntu 18.04.4 LTS   4.15.0-76-generic   containerd://1.3.3-k3s1
n1     Ready    worker   56m   v1.17.2+k3s1   192.168.0.201   <none>        Ubuntu 18.04.4 LTS   4.15.0-76-generic   containerd://1.3.3-k3s1
n2     Ready    worker   55m   v1.17.2+k3s1   192.168.0.202   <none>        Ubuntu 18.04.4 LTS   4.15.0-76-generic   containerd://1.3.3-k3s1
```
```sh
vagrant@m1:~$ kubectl get componentstatus
NAME                 STATUS    MESSAGE   ERROR
scheduler            Healthy   ok
controller-manager   Healthy   ok
```

