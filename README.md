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
1. Obtain the helper files from [here](https://github.com/fxnaranjo/cp4ba-adp/blob/main/helper/ibm-cp-automation-3.1.1.tgz) and untar in a local directory

2. Navigate to the following directory: ibm-cp-automation-3.1.1/ibm-cp-automation/inventory/cp4aOperatorSdk/files/deploy/crs and untar the cert-k8s file
```
#tar -xvf cert-k8s-21.0.2.tar
```

3.Change directory to the cert-kubernetes/descriptors folder
```
#cd cert-kubernetes/descriptors
```

4.Open the cluster_role_binding.yaml file and replace the placeholder string <NAMESPACE> with the target namespace where you want to install the Cloud Pak.
  
5. Apply the cluster_role_binding.yaml and cluster_role.yaml files
```
#oc apply -f cluster_role.yaml
#oc apply -f cluster_role_binding.yaml
```



***
### 7.Installing Filenet using the Cloud Pak for Automation Operator
1. Download the following github repo
```
#git clone https://github.com/icp4a/cert-kubernetes.git
```
2. Run the cluster admin setup script
```
#cd {$REPO_DIRECTORY}/cert-kubernetes/scripts
#./cp4a-clusteradmin-setup.sh 
```
* In this screen select the type of platform, in this case option 1
![Setup1](https://github.com/fxnaranjo/filenet/raw/main/images/1setup.png "Setup1")

* In this step select the type of deployment, in this case option 2
![Setup2](https://github.com/fxnaranjo/filenet/raw/main/images/2setup.png "Setup2")

* Next, enter the name of the namespace to be used for the installation
![Setup3](https://github.com/fxnaranjo/filenet/raw/main/images/3setup.png "Setup3")

* Next, select the user to be used for the installation
![Setup4](https://github.com/fxnaranjo/filenet/raw/main/images/4setup.png "Setup4")

* Next, wait for the script to finish the execution presenting the following screen
![Setup5](https://github.com/fxnaranjo/filenet/raw/main/images/5setup.png "Setup5")

3. Run the operator install and create the Custom Resource Definition
```
#cd {$REPO_DIRECTORY}/cert-kubernetes/scripts
#./cp4a-deployment.sh
```
* Accept the licence
![Opp1](https://github.com/fxnaranjo/filenet/raw/main/images/1operator.png "Operator5")

* Enter either New or Existing installation, in this case New(1)
![Opp2](https://github.com/fxnaranjo/filenet/raw/main/images/2operator.png "Operator2")

* Enter the option to use OLM, in this case No
![Opp3](https://github.com/fxnaranjo/filenet/raw/main/images/3operator.png "Operator3")

* In this step select the type of deployment, in this case option 2
![Setup2](https://github.com/fxnaranjo/filenet/raw/main/images/2setup.png "Setup2")

* In this screen select the type of platform, in this case option 1
![Setup1](https://github.com/fxnaranjo/filenet/raw/main/images/1setup.png "Setup1")

* Select the component of the cloud pak to be installed, in this case option 1
![Opp4](https://github.com/fxnaranjo/filenet/raw/main/images/4operator.png "Operator4")

* In the next screen just hit ENTER because no additional components will be installed
![Opp5](https://github.com/fxnaranjo/filenet/raw/main/images/5operator.png "Operator5")

* Next, you must select the features of filnet to be installed, just hit ENTER because no additional features will be required. By default cpe,graphql and navigator are installed
![Opp6](https://github.com/fxnaranjo/filenet/raw/main/images/6operator.png "Operator6")

* Next, indicate that you already have an Entitlement Registry key
![Opp7](https://github.com/fxnaranjo/filenet/raw/main/images/7operator.png "Operator7")

* Next, enter the Entitlement Registry key
![Opp8](https://github.com/fxnaranjo/filenet/raw/main/images/8operator.png "Operator8")

* Next, enter the Cluster hostname
![Opp9](https://github.com/fxnaranjo/filenet/raw/main/images/9operator.png "Operator9")

* Next, enter the Storage class to be used, in this case: managed-nfs-storage
![Opp10](https://github.com/fxnaranjo/filenet/raw/main/images/10operator.png "Operator10")

* Next, enter the type of LDAP to be used, in this case: Tivoli Directory Server / Security Directory Server
![Opp11](https://github.com/fxnaranjo/filenet/raw/main/images/11operator.png "Operator11")

* Next, enter the number of Object Stores to be created, in this case one
![Opp12](https://github.com/fxnaranjo/filenet/raw/main/images/12operator.png "Operator12")

* Next, review the summary and type Yes to continue with the operator deployment
![Opp13](https://github.com/fxnaranjo/filenet/raw/main/images/13operator.png "Operator13")

* Review the openshift web console until the operator is up and running
![Opp14](https://github.com/fxnaranjo/filenet/raw/main/images/14operator.png "Operator14")

4. Deploy Filenet Custom Resource Definition
* The prior procedure creates a file in {$REPO_DIRECTORY}/cert-kubernetes/scripts/generated-cr called ibm_cp4a_cr_final.yaml, to deploy filenet you have to edit this file in order to include your environment properties.

* The following [file](https://github.com/fxnaranjo/filenet/tree/main/cr) can be used as an example.

* Once the file is ready you can deploy filenet using the following command:
```
#cd {$REPO_DIRECTORY}/cert-kubernetes/scripts/generated-cr/
#oc apply -f ibm_cp4a_cr_final.yaml
```
* Review the openshift web console until all pods are up and running: 2 cpe, 2 graphql , 2 navigator
![Deploy1](https://github.com/fxnaranjo/filenet/raw/main/images/1deploy.png "Deploy1")

* Once the deployment is completed run the following script for final validation
```
#cd {$REPO_DIRECTORY}/cert-kubernetes/scripts/
#./cp4a-post-deployment.sh
```
* The screen will show the URLs for both ACCE and Navigator
![Deploy2](https://github.com/fxnaranjo/filenet/raw/main/images/2deploy.png "Deploy2")

***
***
***
# Congratulations!!, Filenet is now ready to use
