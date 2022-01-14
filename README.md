# Related Repositories

* Base images deployed by this Azure application
  * [tWAS Base](https://github.com/WASdev/azure.websphere-traditional.image/tree/main/twas-base)
  * [tWAS Base trial](https://github.com/WASdev/azure.websphere-traditional.image/tree/main/twas-base-trial)
* [WebSphere traditional cluster](https://github.com/WASdev/azure.websphere-traditional.cluster)
* [Liberty on ARO](https://github.com/WASdev/azure.liberty.aro)
* [Liberty on AKS](https://github.com/WASdev/azure.liberty.aks)

# Deploy RHEL 8.4 VMs on Azure with IBM WebSphere Application Server traditional V9.0.5 single server

## Prerequisites

1. Register an [Azure subscription](https://azure.microsoft.com/).
1. Register an [IBM id](https://www.ibm.com/account/reg/sg-en/signup?formid=urx-19776) and make it entitled if you want to deploy a full-support IBM WebSphere Appliation Server. Otherwise, IBM id is not required if you just want to deploy a trial version instead.
1. The virtual machine offer which includes the image of RHEL 8.4 with IBM WebSphere and JDK pre-installed is used as image reference to deploy virtual machine on Azure. Before the offer goes live in Azure Marketplace, your Azure subscription needs to be added into white list to successfully deploy VM using ARM template of this repo.
1. Install [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
1. Install [PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-7.1).
1. Install [Maven](https://maven.apache.org/download.cgi).
1. Install [`jq`](https://stedolan.github.io/jq/download/).

## Steps of deployment

1. Checkout [azure-javaee-iaas](https://github.com/Azure/azure-javaee-iaas)
   1. Change to directory hosting the repo project & run `mvn clean install`
1. Checkout [arm-ttk](https://github.com/Azure/arm-ttk) under the specified parent directory
1. Checkout this repo under the same parent directory and change to directory hosting the repo project
1. Build the project by replacing all placeholder `${<place_holder>}` with valid values
   1. Deploy a full-support IBM WebSphere Appliation Server with an entitled IBM id

      ```bash
      mvn -Dgit.repo=<repo_user> -Dgit.tag=<repo_tag> -DuseTrial=false -DibmUserId=<entitledIbmUserId> -DibmUserPwd=<entitledIbmUserPwd> -DvmSize=<vmSize> -DdnsLabelPrefix=<dnsLabelPrefix> -DadminUsername=<adminUsername> -DadminPasswordOrKey=<adminPassword|adminSSHPublicKey> -DauthenticationType=<password|sshPublicKey> -DwasUsername=<wasUsername> -DwasPassword=<wasPassword> -Dtest.args="-Test All" -Ptemplate-validation-tests clean install
      ```

    1. Deploy a trial version of IBM WebSphere Appliation Server

       ```bash
       mvn -Dgit.repo=<repo_user> -Dgit.tag=<repo_tag> -DuseTrial=true -DvmSize=<vmSize> -DdnsLabelPrefix=<dnsLabelPrefix> -DadminUsername=<adminUsername> -DadminPasswordOrKey=<adminPassword|adminSSHPublicKey> -DauthenticationType=<password|sshPublicKey> -DwasUsername=<wasUsername> -DwasPassword=<wasPassword> -Dtest.args="-Test All" -Ptemplate-validation-tests clean install
       ```

1. Change to `./target/cli` directory
1. Using `deploy.azcli` to deploy

   ```bash
   ./deploy.azcli -n <deploymentName> -i <subscriptionId> -g <resourceGroupName> -l <resourceGroupLocation>
   ```

## After deployment

1. If you check the resource group in [azure portal](https://portal.azure.com/), you will see one VM and related resources created
1. To open IBM WebSphere Integrated Solutions Console in browser for further administration:
   1. Login to Azure Portal
   1. Open the resource group you specified to deploy WebSphere Cluster
   1. Navigate to "Deployments > specified_deployment_name > Outputs"
   1. Copy value of property `adminSecuredConsole` and browse it with credentials you specified in cluster creation
1. To visit sevlets of `DefaultApplication` which is installed in the server by default
   1. Copy value of property `snoopServletUrl` and open it in your browser
   1. Copy value of property `hitCountServletUrl` and open it in your browser
