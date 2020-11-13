## IBM Kubernetes Cluster Set up

### Setup your CLI Tools:

Make sure you have the IBM Cloud CLI, Kubernetes Service plug-in(```ibmcloud ks```), and Kubernetes CLI (```kubectl```) installed. Also, make sure your version of _kubectl_ is the same as that you used in creating your cluster (1.18.10 in my case). You can check that using: ```kubectl version --short```. You can find more details on installing these tools [here](https://cloud.ibm.com/docs/containers?topic=containers-cs_cli_install)

```
# To install Kubernetes Service plug-in
$ ibmcloud plugin install kubernetes-service
```

### Accessing your cluster

1. Log in to your IBM Cloud account (if you're not already logged in). _us-east_ is the correct region for your account.

```
$ ibmcloud login -a cloud.ibm.com -r us-east -g Default
```

2. Set up the Kubernetes context to your cluster for _this_ terminal session (xxxxxxxxxxx is the specific Cluster ID of the cluster you created which you can access through the IBM Cloud console.):

```
$ ibmcloud ks cluster config --cluster xxxxxxxxxxx
```

3. Verify that you can connect to your cluster

```
$ kubectl config current-context
mycluster-tor01-rosibm/xxxxxxxxxxx
```


### Obtaining Service Endpoint URL

Get details about your cluster:

```
ibmcloud ks cluster get -c xxxxxxxxxxx
```

Note down the _Public Service Endpoint URL_ and _Private Service Sendpoint URL_ if you need them at any point.