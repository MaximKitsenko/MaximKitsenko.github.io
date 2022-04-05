# Use Multiple configs for k8s

Few weeks ago we decide to migrate to new cloud provider, since our services are deployed on k8s we faced with migration from one k8s kluster to another. The first thing after creating new cluster was configuring kubectl to work with both clusters. It turned out it was not so easy. Here i will describe basic steps and few pitfalls.

The following instructions are for `Windows` but they should be the same for `Linux`, except syntax.


Create backup of an old config
```
Copy-Item "C:\Users\macro\.kube\config" -Destination "C:\Users\macro\.kube\config_backup"
```
// Set environment variable with src configs pathes
$Env:KUBECONFIG="C:\Users\macro\.kube\new-env-k8s.yaml;C:\Users\macro\.kube\old-env-k8s.yaml"

// !!! This doesn't work
//$Env:KUBECONFIG="C:\Users\macro\.kube\old-env-k8s.yaml;C:\Users\macro\.kube\new-env-k8s.yaml"

// Make sure env variable contains correct value
$ENV:KUBECONFIG

// Generate result config which contains both clusters
kubectl config view --flatten > "C:\Users\macro\.kube\config-merged"

// Another ways to do it
// kubectl config view --merge --flatten | Out-File "C:\Users\macro\.kube\newconfig_merge"
// kubectl config view  --raw > "C:\Users\macro\.kube\newconfig_raw"

// Make sure output file contains configurations from both clusters
// You should see somethnig like this:
// NAME
// cloud1-k8s
// do-ams3-cloud2-k8s
kubectl --kubeconfig="C:\Users\macro\.kube\config-merged" config get-clusters
```

## Pitfalls

if your `config-merged` doesn't include both k8s configs, try to reorder files in:
```
$Env:KUBECONFIG="C:\Users\macro\.kube\old-env-k8s.yaml;C:\Users\macro\.kube\new-env-k8s.yaml"
```

Also if your files are not in `.kube\` try to put them here.
