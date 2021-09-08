# IBM Automation Document Processing(IBM CLOUD)

This article is intended to guide readers into an easy installation of an IBM Automation Document Processing instance using IBM Cloud managed Openshift Cluster.
This steps are for demonstrations only and not intended for an actual environment. Security and performance considerations must be addressed for each component in any installation

***
### 1.ARCHITECTURE

IBM Cloud components used were:
1. An Openshift cluster of three worker nodes (16x32) version 4.6.x (You must use version 4.6)
2. A configured storage class to handle RWX(read,write,many) volumes of at least 500 GB

***
### 2.Preparing the ADP namespace and REDHAT OPENSHIFT CLUSTER (ROKS)
#### For this step you must use a terminal console with the oc command line tool installed
#### For this step you must be logged in into REDHAT OPENSHIFT CLUSTER (ROKS)
```
#oc new-project adp
```

#### Creata a secret to access IBM Container regisrty, You can obtain this key using this procedure:
* Log in to [MyIBM Container Software Library](https://myibm.ibm.com/products-services/containerlibrary) with the IBMid and password that is associated with the entitled software.
* In the Container software library tile, verify your entitlement on the View library page, and then go to Get entitlement key to retrieve the key. 

```
#oc create secret docker-registry ibm-entitlement-key --docker-username=cp --docker-password=<token> --docker-server=cp.icr.io -n filenet
```
***
1.Obtain the helper files from [here](https://github.com/fxnaranjo/cp4ba-adp/blob/main/helper/ibm-cp-automation-3.1.1.tgz) and untar in a local directory

2.Navigate to the following directory: ibm-cp-automation-3.1.1/ibm-cp-automation/inventory/cp4aOperatorSdk/files/deploy/crs and untar the cert-k8s file
```
#tar -xvf cert-k8s-21.0.2.tar
```

3.Change directory to the cert-kubernetes/descriptors folder
```
#cd cert-kubernetes/descriptors
```

4.Open the cluster_role_binding.yaml file and replace the placeholder string <NAMESPACE> with the target namespace where you want to install the Cloud Pak.
 
5.Apply the cluster_role_binding.yaml and cluster_role.yaml files
```
#oc apply -f cluster_role.yaml
#oc apply -f cluster_role_binding.yaml
```
  
6.Create the ibm-cp4ba-privileged and ibm-cp4ba-anyuid service accounts (SA), and bind the security context constraints (SCC) to control the actions the SA can take and what it can access, you can obtain the service-account-for-demo.yaml from [here](https://github.com/fxnaranjo/cp4ba-adp/blob/main/helper/service-account-for-demo.yaml)
```
#oc apply -f service-account-for-demo.yaml -n ${NAMESPACE}
#oc adm policy add-scc-to-user privileged -z ibm-cp4ba-privileged -n ${NAMESPACE}
#oc adm policy add-scc-to-user anyuid -z ibm-cp4ba-anyuid -n ${NAMESPACE}
```

 7.Change directory to the scripts folder and run the cluster setup script and follow the prompts in the command window
 ```
 #cd ../scripts
 #./cp4a-clusteradmin-setup.sh
 ```
  
* In this screen select the type of platform, in this case option 1
![Cluster1](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/cluster1.png "Cluster1")



***
***
***
# Congratulations!!, ADP is now ready to use
