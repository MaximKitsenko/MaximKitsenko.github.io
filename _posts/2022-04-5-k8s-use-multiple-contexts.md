# Use Multiple configs for k8s

Few weeks ago we decided to migrate our application to the new cloud provider, since our services are deployed on k8s we faced with migration from one k8s kluster to another. The first thing after creating new cluster was configuring kubectl to work with both clusters. It turned out it was not so easy. Here i will describe basic steps and few pitfalls.

The following instructions are for `Windows` but they should be the same for `Linux`, except syntax.


1. Create backup of an old config:
```
Copy-Item "C:\Users\macro\.kube\config" -Destination "C:\Users\macro\.kube\config_backup"
```

2. Set environment variable with src configs pathes:
```
$Env:KUBECONFIG="C:\Users\macro\.kube\new-env-k8s.yaml;C:\Users\macro\.kube\old-env-k8s.yaml"
// I dont know why but this doesn't work
//$Env:KUBECONFIG="C:\Users\macro\.kube\old-env-k8s.yaml;C:\Users\macro\.kube\new-env-k8s.yaml"
```

3. Make sure env variable contains correct value:
```
$ENV:KUBECONFIG
```

4. Generate result config which contains both clusters:
```
kubectl config view --flatten > "C:\Users\macro\.kube\config-merged"
```

    1. Another way to do it:
    ```
    kubectl config view --merge --flatten | Out-File "C:\Users\macro\.kube\newconfig_merge"
    ```
    2. or
    ```
    kubectl config view  --raw > "C:\Users\macro\.kube\newconfig_raw"
    ```

5. Make sure output file contains configurations from both clusters:
```Bash
kubectl --kubeconfig="C:\Users\macro\.kube\config-merged" config get-clusters
```

You should see somethnig like this:

![_config.yml]({{ site.baseurl }}/images/2022-04-5-k8s-use-multiple-contexts\2022-04-05_14-54-25-blured.png)

6. The last step - replace current config with new one:
```Cmd
Copy-Item "C:\Users\macro\.kube\config-merged" -Destination "C:\Users\macro\.kube\config"
```

## Pitfalls

if your `config-merged` doesn't include both k8s configs, try to reorder files in:
~~~
$Env:KUBECONFIG="C:\Users\macro\.kube\old-env-k8s.yaml;C:\Users\macro\.kube\new-env-k8s.yaml"
~~~

Also if your files are not in `.kube\` try to put them here.

## Other useful commands

```Shell
kubectl config get-contexts

kubectl config current-context

kubectl config view 

kubectl config use-context do-ams3-company-k8s

kubectl edit deployment api-loss-classifier
```
