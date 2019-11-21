---
title: Service Fabric Azure Files Volume Driver (GA) | Microsoft Docs
description: O Service Fabric oferece suporte a Arquivos do Azure para volumes de backup de seu contêiner.
services: service-fabric
author: athinanthny
manager: chackdan
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.topic: conceptual
ms.date: 6/10/2018
ms.author: atsenthi
ms.openlocfilehash: 168e4327dc66474b2e43afdec091c5a44c1399eb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229271"
---
# <a name="service-fabric-azure-files-volume-driver"></a>Service Fabric Azure Files Volume Driver
The Azure Files volume plugin, a [Docker volume plugin](https://docs.docker.com/engine/extend/plugins_volume/) that provides [Azure Files](/azure/storage/files/storage-files-introduction) based volumes for Docker containers is now **GA (Generally Available)** .

Este plug-in de volume do Docker é empacotado como um aplicativo do Service Fabric que pode ser implantado em clusters do Service Fabric. Seu objetivo é fornecer Arquivos do Azure com base em volumes para outros aplicativos de contêiner do Service Fabric que são implantados no cluster.

> [!NOTE]
> Version 6.5.661.9590 of the Azure Files volume plugin is a GA(Generally available) release. 
>

## <a name="prerequisites"></a>Pré-requisitos
* A versão do Windows do plug-in de volume dos Arquivos do Azure funciona no [Windows Server versão 1709](/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 versão 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) ou apenas em sistemas operacionais posteriores.

* A versão do Linux do plug-in de volume dos Arquivos do Azure funciona em todas as versões de sistema operacional com suporte pelo Service Fabric.

* O plug-in do volume de arquivos do Azure funciona somente no Service Fabric versão 6.2 e mais recente.

* Siga as instruções na [Documentação dos Arquivos do Azure](/azure/storage/files/storage-how-to-create-file-share) para criar um compartilhamento de arquivo para o aplicativo de contêiner do Service Fabric para usar como o volume.

* Você precisará do [Powershell com o módulo do Service Fabric](/azure/service-fabric/service-fabric-get-started) ou [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) instalado.

* If you are using Hyper-V containers, the following snippets need to be added in the ClusterManifest (local cluster) or fabricSettings section in your Azure Resource Manager template (Azure cluster) or ClusterConfig.json (standalone cluster).

No ClusterManifest, o seguinte precisa ser adicionado na seção Hospedagem. In this example, the volume name is **sfazurefile** and the port it listens to on the cluster is **19100**. Replace them with the correct values for your cluster.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

In the fabricSettings section in your Azure Resource Manager template (for Azure deployments) or ClusterConfig.json (for standalone deployments), the following snippet needs to be added. Again, replace the volume name and port values with your own.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```


## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Deploy a sample application using Service Fabric Azure Files volume driver

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Using Azure Resource Manager via the provided Powershell script (recommended)

If your cluster is based in Azure, we recommend deploying applications to it using the Azure Resource Manager application resource model for ease of use and to help move towards the model of maintaining infrastructure as code. This approach eliminates the need to keep track of the app version for the Azure Files volume driver. It also enables you to maintain separate Azure Resource Manager templates for each supported OS. The script assumes you are deploying the latest version of the Azure Files application and takes parameters for OS type, cluster subscription ID, and resource group. You can download the script from the [Service Fabric download site](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip). Note that this automatically sets the ListenPort, which is the port on which the Azure Files volume plugin listens for requests from the Docker daemon, to 19100. You can change it by adding parameter named "listenPort". Ensure that the port does not conflict with any other port that the cluster or your applications uses.
 

Azure Resource Manager deployment command for Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Azure Resource Manager deployment command for Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Once you've successfully run the script, you can skip to the [configuring your application section.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Manual deployment for standalone clusters

The Service Fabric application that provides the volumes for your containers can be downloaded from the [Service Fabric download site](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.516.9494.zip). O aplicativo pode ser implantado para o cluster por meio do [PowerShell](./service-fabric-deploy-remove-applications.md), [CLI](./service-fabric-application-lifecycle-sfctl.md) ou [APIs FabricClient](./service-fabric-deploy-remove-applications-fabricclient.md).

1. Using the command line, change directory to the root directory of the downloaded application package.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Next, copy the application package to the image store  with the appropriate values for [ApplicationPackagePath] and [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Registrar o tipo de aplicativo

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Create the application, paying close attention to the **ListenPort** application parameter value. This value is the port on which the Azure Files volume plugin listens for requests from the Docker daemon. Ensure that the port provided to the application matches the VolumePluginPorts in the ClusterManifest and does not conflict with any other port that the cluster or your applications uses.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> O Windows Server 2016 Datacenter não é compatível com mapeamento de montagens de SMB em contêineres ([só há compatibilidade no Windows Server versão 1709](/virtualization/windowscontainers/manage-containers/container-storage)). Essa restrição impede mapeamento do volume de rede e drivers de volume de Arquivos do Azure em versões anteriores à 1709.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Implantar o aplicativo em um cluster de desenvolvimento local
Follow steps 1-3 from the [above.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters)

 A contagem de instâncias de serviço padrão para o aplicativo de plug-in de volume dos Arquivos do Azure é -1, o que significa que há uma instância do serviço implantado em cada nó no cluster. No entanto, ao implantar o aplicativo de plug-in de volume dos Arquivos do Azure em um cluster de desenvolvimento local, a contagem de instâncias de serviço deve ser especificada como 1. Isso pode ser feito por meio do parâmetro de aplicativo **InstanceCount**. Therefore, the command for creating the Azure Files volume plugin application on a local development cluster is:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Configure seus aplicativos para usar o volume
The following snippet shows how an Azure Files based volume can be specified in the application manifest file of your application. O elemento específico de interesse é a marca **Volume**:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

O nome do driver para o plug-in de volume dos Arquivos do Azure é **sfazurefile**. This value is set for the **Driver** attribute of the **Volume** tag element in the application manifest.

In the **Volume** tag in the snippet above, the Azure Files volume plugin requires the following attributes:
- **Source** – Esse é o nome do volume. O usuário pode escolher qualquer nome para seu volume.
- **Destination** - This attribute is the location that the volume is mapped to within the running container. Assim, seu destino não pode ser um local já existente em seu contêiner

Conforme exibido nos elementos **DriverOption** no snippet acima, o plug-in do volume de Arquivos do Azure oferece suporte às seguintes opções de driver:
- **shareName** - Nome do compartilhamento de arquivos dos Arquivos do Azure que fornece o volume para o contêiner.
- **storageAccountName** - Nome da conta de armazenamento do Azure que contém o compartilhamento de arquivos dos Arquivos do Azure.
- **storageAccountKey** - Chave de acesso para a conta de armazenamento do Azure que contém o compartilhamento de arquivos dos Arquivos do Azure.
- **storageAccountFQDN** – nome de domínio associado à conta de armazenamento. Se storageAccountFQDN não for especificado, o nome de domínio será formado usando o padrão suffix(.file.core.windows.net) com storageAccountName.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Usando seu próprio volume ou o log do driver
O Service Fabric também permite o uso do [volume](https://docs.docker.com/engine/extend/plugins_volume/) personalizado ou drivers de [registro em log](https://docs.docker.com/engine/admin/logging/overview/). Se o driver de volume/log do Docker não estiver instalado em seu cluster, é possível instalá-lo manualmente usando os protocolos RDP/SSH. Você pode executar a instalação usando esses protocolos por meio de um [script de inicialização de conjunto de escala de máquina virtual](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) ou um [script SetupEntryPoint](/azure/service-fabric/service-fabric-application-model).

Um exemplo de script para instalar o [driver de volume do Docker para Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) é o seguinte:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Em seus aplicativos, para usar o volume ou a unidade de log que você instalou, é necessário especificar os valores apropriados nos elementos **Volume** e **LogConfig** sob  **ContainerHostPolicies** em seu manifesto de aplicativo.

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

Ao especificar um plug-in do volume, o Service Fabric cria automaticamente o volume usando os parâmetros especificados. A marcação **Fonte** para o elemento **Volume** é o nome do volume e a marcação **Driver** especifica o plug-in do driver do volume. A marcação **Destino** é o local em que a **Fonte** é mapeada no contêiner em execução. Assim, seu destino não pode ser um local já existente em seu contêiner. As opções podem ser especificadas usando a marcação de **DriverOption** como da seguinte maneira:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Os parâmetros do aplicativo são compatíveis com volumes, conforme mostrado no snippet de manifesto anterior (procure `MyStorageVar` para obter um uso de exemplo).

Se um driver de log do Docker for especificado, será necessário implantar agentes (ou contêineres) para tratar os logs no cluster. A marcação **DriverOption** pode ser usada para especificar opções para o driver de log.

## <a name="next-steps"></a>Próximos passos
* Para ver exemplos de contêiner, incluindo o driver de volume, visite os [Exemplos de contêiner do Service Fabric](https://github.com/Azure-Samples/service-fabric-containers)
* Para implantar contêineres em um cluster do Service Fabric, consulte o artigo [Implantar um contêiner no Service Fabric](service-fabric-deploy-container.md)
