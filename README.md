# IBM Automation Document Processing - IBM Cloud Pak for Business Automation (IBM CLOUD)

This article is intended to guide readers into an easy installation of an IBM Automation Document Processing instance using IBM Cloud managed Openshift Cluster.
This steps are for demonstrations only and not intended for a production environment. Security and performance considerations must be addressed for each component in any installation

***
### 1.ARCHITECTURE

IBM Cloud components used were:
1. An Openshift cluster of three worker nodes (16x32) version 4.6.x (You must use version 4.6)
2. A configured storage class to handle RWX(read,write,many) volumes of at least 500 GB

***
### 2.Preparing the ADP namespace and REDHAT OPENSHIFT CLUSTER (ROKS) / Install the operator
#### For this step you must use a terminal console with the oc command line tool installed
#### For this step you must be logged in into REDHAT OPENSHIFT CLUSTER (ROKS)
```
#oc new-project adp
```

#### Creata a secret to access IBM Container regisrty, You can obtain this key using this procedure:
* Log in to [MyIBM Container Software Library](https://myibm.ibm.com/products-services/containerlibrary) with the IBMid and password that is associated with the entitled software.
* In the Container software library tile, verify your entitlement on the View library page, and then go to Get entitlement key to retrieve the key. 

```
#oc create secret docker-registry ibm-entitlement-key --docker-username=cp --docker-password=<token> --docker-server=cp.icr.io -n adp
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
![Cluster2](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/cluster1.png "Cluster1")
 
* In this screen select the type of deployment to use, in this case option 1
![Cluster2](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/cluster2.png "Cluster2")
 
* Next enter the name of the project to use, in this case "adp"
![Cluster3](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/cluster3.png "Cluster3")
 
* Next enter the user to make the deployment, in this case choose your assigned IAM#User
![Cluster4](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/cluster4.png "Cluster4")
 
* Next, indicate that you already have an Entitlement Registry key
![Cluster5](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/cluster5.png "Cluster5")

* Next, enter the Entitlement Registry key 
![Cluster6](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/cluster6.png "Cluster6")
 
* Next, enter the name of the storage class to be used for the operator
![Cluster7](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/cluster7.png "Cluster7")

* The operator installation will start, the process will take about 15 minutes, check the adp and ibm-common-services namespaces for running pods
 
***
### 3.Instaling the ADP component in Cloud Pak for Business Automation
#### For this step you must use a terminal console with the oc command line tool installed
#### For this step you must be logged in into REDHAT OPENSHIFT CLUSTER (ROKS)
 
* In the same directory run the deployment script and follow the prompts in the command window
 ```
 #./cp4a-deployment.sh
```
 
* In the first screen hit ENTER and ACCEPT the IBM Cloud Pak for Business Automation license
![Adp1](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/adp1.png "Adp1")
 
* Next select the type of installation, in this case "New"
![Adp2](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/adp2.png "Adp2")

* Next select the type of deployment, in this case "Demo"
![Adp3](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/adp3.png "Adp3")

* Next select the platform, in this case "ROKS"
![Adp4](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/adp4.png "Adp4")

* Select the Cloud Pak for Business Automation capability to install, in this case # 6
![Adp5](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/adp5.png "Adp5")
 
* Next just hit ENTER to continue without additional capabilities
![Adp6](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/adp6.png "Adp6")

* Next answer "No" to the question about CPE storage
![Adp7](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/adp7.png "Adp7")
 
* Next just hit ENTER to continue without optional components
![Adp8](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/adp8.png "Adp8")
 
* Enter the storage class for slow,medium,fast volumnes
![Adp9](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/adp9.png "Adp9")

* Next answer "No" to the question about GPU
![Adp10](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/adp10.png "Adp10")
 
* Review the summary and answer "Yes" to begin the installation
![Adp11](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/adp11.png "Adp11")

* The ADP installation will start, the process will take about 3.5 hours, check the adp and ibm-common-services namespaces for running pods
 
* You can check the operator logs with this command
```
#oc logs deployment/ibm-cp4a-operator -c operator -f
```
 
* The final running pods in the adp project<br/><br/>
![Adp12](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/adp12.png "Adp12")
 
* The final running pods in the ibm-common-services project<br/><br/>
![Adp13](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/adp13.png "Adp13")
 
***
### 4.Obtaining the ADP URLs and credentials
#### For this step you must use a terminal console with the oc command line tool installed
#### For this step you must be logged in into REDHAT OPENSHIFT CLUSTER (ROKS)

* In the same directory run the post-deployment script
 ```
 #./cp4a-post-deployment.sh
```

* The screen will show you all the important URLs and credentials
![Adp14](https://github.com/fxnaranjo/cp4ba-adp//raw/main/images/adp14.png "Adp14")
 
***
***
***
# Congratulations!!, ADP is now ready to use
