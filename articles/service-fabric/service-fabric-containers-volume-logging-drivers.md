---
title: Driver de volume de arquivos do Azure para malha de serviço
description: O Service Fabric oferece suporte a Arquivos do Azure para volumes de backup de seu contêiner.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: 514a0cb12359d58e38ebc30ae12cdb277757f2b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750049"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Driver de volume de arquivos do Azure para malha de serviço

O driver de volume azure Files é um [plugin de volume do Docker](https://docs.docker.com/engine/extend/plugins_volume/) que fornece volumes baseados em arquivos [Azure](/azure/storage/files/storage-files-introduction) para contêineres Docker. Ele é embalado como um aplicativo de malha de serviço que pode ser implantado em um cluster service fabric para fornecer volumes para outras aplicações de contêiner service fabric dentro do cluster.

> [!NOTE]
> A versão 6.5.661.9590 do plugin de volume do Azure Files foi liberada para disponibilidade geral.
>

## <a name="prerequisites"></a>Pré-requisitos
* A versão do Windows do plug-in de volume dos Arquivos do Azure funciona no [Windows Server versão 1709](/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 versão 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) ou apenas em sistemas operacionais posteriores.

* A versão do Linux do plug-in de volume dos Arquivos do Azure funciona em todas as versões de sistema operacional com suporte pelo Service Fabric.

* O plug-in do volume de arquivos do Azure funciona somente no Service Fabric versão 6.2 e mais recente.

* Siga as instruções na [Documentação dos Arquivos do Azure](/azure/storage/files/storage-how-to-create-file-share) para criar um compartilhamento de arquivo para o aplicativo de contêiner do Service Fabric para usar como o volume.

* Você precisará do [Powershell com o módulo do Service Fabric](/azure/service-fabric/service-fabric-get-started) ou [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) instalado.

* Se você estiver usando contêineres Hyper-V, os seguintes trechos devem ser adicionados na seção ClusterManifest (cluster local) ou fabricSettings no modelo Azure Resource Manager (cluster Azure) ou ClusterConfig.json (cluster autônomo).

No ClusterManifest, o seguinte precisa ser adicionado na seção Hospedagem. Neste exemplo, o nome do volume é **sfazurefile** e a porta que ele ouve no cluster é **19100**. Substitua-os pelos valores corretos para o seu cluster.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

Na seção Configurações de malha no modelo do Azure Resource Manager (para implantações do Azure) ou no ClusterConfig.json (para implantações autônomas), o seguinte trecho precisa ser adicionado. Novamente, substitua os valores de nome e porta de volume por seus próprios.

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

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Implantar um aplicativo de exemplo usando o driver de volume de arquivos do Service Fabric Azure

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Usando o Azure Resource Manager através do script Powershell fornecido (recomendado)

Se o seu cluster estiver baseado no Azure, recomendamos a implantação de aplicativos para ele usando o modelo de recurso de aplicativo do Azure Resource Manager para facilitar o uso e para ajudar a avançar para o modelo de manutenção da infra-estrutura como código. Essa abordagem elimina a necessidade de acompanhar a versão do aplicativo para o driver de volume de arquivos Do Azure. Ele também permite que você mantenha modelos separados do Azure Resource Manager para cada sistema operacional suportado. O script pressupõe que você está implantando a versão mais recente do aplicativo Azure Files e leva parâmetros para o tipo de SISTEMA OPERACIONAL, ID de assinatura de cluster e grupo de recursos. Você pode baixar o script no site de [download service fabric](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip). Observe que isso define automaticamente o ListenPort, que é a porta na qual o plugin de volume do Azure Files ouve solicitações do daemon Docker, até 19100. Você pode alterá-lo adicionando parâmetro chamado "listenPort". Certifique-se de que a porta não conflita com nenhuma outra porta que o cluster ou seus aplicativos usam.
 

Comando de implantação do Azure Resource Manager para Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Comando de implantação do Azure Resource Manager para Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Depois de executar o script com sucesso, você pode pular para a [configuração da seção de aplicação.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Implantação manual para clusters autônomos

O aplicativo Service Fabric que fornece os volumes para seus contêineres pode ser baixado no site de [download service fabric](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip). O aplicativo pode ser implantado para o cluster por meio do [PowerShell](./service-fabric-deploy-remove-applications.md), [CLI](./service-fabric-application-lifecycle-sfctl.md) ou [APIs FabricClient](./service-fabric-deploy-remove-applications-fabricclient.md).

1. Usando a linha de comando, altere o diretório para o diretório raiz do pacote de aplicativos baixado.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Em seguida, copie o pacote do aplicativo para o armazenamento de imagens com os valores apropriados para [ApplicationPackagePath] e [ImageStoreConnectionString]:

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

4. Crie o aplicativo, prestando muita atenção ao valor do parâmetro do aplicativo **ListenPort.** Este valor é a porta na qual o plugin de volume do Azure Files ouve solicitações do daemon Docker. Certifique-se de que a porta fornecida ao aplicativo corresponda ao VolumePluginPorts no ClusterManifest e não entre em conflito com nenhuma outra porta que o cluster ou seus aplicativos usem.

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
Siga os passos 1-3 do [acima.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters)

 A contagem de instâncias de serviço padrão para o aplicativo de plug-in de volume dos Arquivos do Azure é -1, o que significa que há uma instância do serviço implantado em cada nó no cluster. No entanto, ao implantar o aplicativo de plug-in de volume dos Arquivos do Azure em um cluster de desenvolvimento local, a contagem de instâncias de serviço deve ser especificada como 1. Isso pode ser feito por meio do parâmetro de aplicativo **InstanceCount**. Portanto, o comando para criar o aplicativo de plugin de volume do Azure Files em um cluster de desenvolvimento local é:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Configure seus aplicativos para usar o volume
O trecho a seguir mostra como um volume baseado em Arquivos Azure pode ser especificado no arquivo manifesto de aplicativo do seu aplicativo. O elemento específico de interesse é a marca **Volume**:

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

O nome do driver para o plugin de volume azure Files é **sfazurefile**. Este valor é definido para o atributo **Driver** do elemento de marca **Volume** no manifesto do aplicativo.

Na **tag Volume** no trecho acima, o plugin de volume Azure Files requer os seguintes atributos:
- **Source** – Esse é o nome do volume. O usuário pode escolher qualquer nome para seu volume.
- **Destino** - Este atributo é o local para o local para o que o volume é mapeado dentro do contêiner em execução. Assim, seu destino não pode ser um local já existente em seu contêiner

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

Em seus aplicativos, para usar o volume ou a unidade de log que você instalou, é necessário especificar os valores apropriados nos elementos **Volume** e **LogConfig** sob ** ContainerHostPolicies** em seu manifesto de aplicativo.

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

## <a name="next-steps"></a>Próximas etapas
* Para ver exemplos de contêiner, incluindo o driver de volume, visite os [Exemplos de contêiner do Service Fabric](https://github.com/Azure-Samples/service-fabric-containers)
* Para implantar contêineres em um cluster de malha de serviço, consulte o artigo [Implantar um contêiner na malha de serviço](service-fabric-deploy-container.md)
