---
title: Habilite a criptografia de disco para clusters Windows
description: Este artigo descreve como habilitar a criptografia de disco para os números do cluster Azure Service Fabric usando o Azure Key Vault no Azure Resource Manager.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251813"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Habilite a criptografia de disco para os ádenos de cluster do Azure Service Fabric no Windows 
> [!div class="op_single_selector"]
> * [Criptografia de Disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Criptografia de Disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Neste tutorial, você aprenderá como ativar a criptografia de disco nos ádenos de cluster Service Fabric no Windows. Você precisará seguir essas etapas para cada um dos tipos de nó e conjuntos de escala de máquinavirtual. Para criptografar os nós, usaremos o recurso de criptografia de disco do Azure em conjuntos de escala de máquinas virtuais.

O guia abrange os seguintes tópicos:

* Conceitos-chave a serem cientes ao habilitar a criptografia de disco no conjunto de escala de máquina virtual do cluster Service Fabric no Windows.
* Passos a serem seguidos antes de ativar a criptografia de disco nos ádeis de cluster service fabric no Windows.
* Passos a serem seguidos para habilitar a criptografia de disco nos ádeis de cluster Service Fabric no Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

**Auto-registro** 

A visualização de criptografia de disco para o conjunto de escala seleção de máquina virtual requer auto-registro. Use as seguintes etapas: 

1. Primeiro, execute o seguinte comando:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Aguarde cerca de 10 minutos até que o status leia *Registrado*. Você pode verificar o status executando o seguinte comando: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Cofre de Chaves Azure** 

1. Crie um cofre de chaves na mesma assinatura e região do conjunto de escalas e selecione a diretiva de acesso **EnabledForDiskEncryption** no cofre de chaves usando seu cmdlet PowerShell. Você também pode definir a política usando a ui do Key Vault no portal Azure com o seguinte comando:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Instale a versão mais recente do [Azure CLI](/cli/azure/install-azure-cli), que tem os novos comandos de criptografia.
3. Instale a versão mais recente do [Azure SDK da versão do Azure PowerShell.](https://github.com/Azure/azure-powershell/releases) A seguir estão os cmdlets de criptografia de disco virtual do Azure Disk Encryption para habilitar[(conjunto)](/powershell/module/az.compute/set-azvmssdiskencryptionextension)o status de criptografia[e](/powershell/module/az.compute/get-azvmssvmdiskencryption)remover[(desativar)](/powershell/module/az.compute/disable-azvmssdiskencryption)criptografia na instância de conjunto de escalas.

| Comando | Versão |  Fonte  |
| ------------- |-------------| ------------|
| Get-AzVmsSDiskEncryptionStatus   | 1.0.0 ou posterior | Az.Compute |
| Get-AzVmssVDiskEncryptionStatus   | 1.0.0 ou posterior | Az.Compute |
| Desativar-AzVmsSDiskEncryption   | 1.0.0 ou posterior | Az.Compute |
| Get-AzVmsSDiskEncryption   | 1.0.0 ou posterior | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 ou posterior | Az.Compute |
| Set-AzVmsSExtensão decriptografia   | 1.0.0 ou posterior | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Cenários com suporte para criptografia de disco
* A criptografia para conjuntos de escala de máquinas virtuais é suportada apenas para conjuntos de escala criados com discos gerenciados. Não é suportado para conjuntos de escala de discos nativos (ou não gerenciados).
* A criptografia é suportada para sO e volumes de dados em conjuntos de escala de máquinavirtual no Windows. A criptografia desativada também é suportada para sO e volumes de dados para conjuntos de escala de máquinavirtual no Windows.
* As operações de reimagem e upgrade de máquinas virtuais para conjuntos de escala de máquinas virtuais não são suportadas na visualização atual.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Crie um novo cluster e habilite a criptografia de disco

Use os comandos a seguir para criar um cluster e habilitar a criptografia de disco usando um modelo do Azure Resource Manager e um certificado autoassinado.

### <a name="sign-in-to-azure"></a>Entrar no Azure 
Faça login com os seguintes comandos:
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Usar um modelo personalizado que você já tem 

Se você precisar criar um modelo personalizado para atender às suas necessidades, recomendamos que você comece com um dos modelos disponíveis na página amostras de [modelo de criação de cluster do Azure Service Fabric.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) Para personalizar a seção [de modelo de cluster,][customize-your-cluster-template] consulte a seguinte orientação.

Se você já tiver um modelo personalizado, verifique duas vezes se todos os três parâmetros relacionados ao certificado no modelo e no arquivo de parâmetros são nomeados da seguinte forma e que os valores são nulos da seguinte forma:

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


```azurecli

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Implantar um aplicativo para um cluster de malha de serviço no Windows
Para implantar um aplicativo no cluster, siga as etapas e orientações no [Deploy e remova aplicativos usando o PowerShell](service-fabric-deploy-remove-applications.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Habilite a criptografia de disco para os conjuntos de escala de máquina virtual criados anteriormente

Para habilitar a criptografia de disco para os conjuntos de escala de máquina virtual criados pelas etapas anteriores, execute os seguintes comandos:
 
```powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Valide se a criptografia de disco estiver habilitada para uma escala de máquina virtual definida no Windows
Obtenha o status de um conjunto de escala de máquina virtual inteiro ou qualquer instância em uma escala definida executando os seguintes comandos.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Além disso, você pode entrar no conjunto de escalas da máquina virtual e certificar-se de que as unidades estão criptografadas.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Desativar a criptografia de disco para uma escala de máquina virtual definida em um cluster Service Fabric 
Desabilite a criptografia de disco para uma escala de máquina virtual definida executando os seguintes comandos. Observe que desativar a criptografia de disco se aplica a todo o conjunto de escala da máquina virtual e não a uma instância individual.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Próximas etapas
Neste ponto, você deve ter um cluster seguro e saber como ativar e desativar a criptografia de disco para os nomes de cluster service fabric e conjuntos de escala de máquina virtual. Para obter orientações semelhantes sobre nós de cluster service fabric no Linux, consulte [Criptografia de disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
