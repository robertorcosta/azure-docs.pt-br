---
title: Habilite a criptografia de disco para clusters Linux
description: Este artigo descreve como habilitar a criptografia de disco para nós de cluster do Azure Service Fabric no Linux usando o Azure Resource Manager e o Azure Key Vault.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: c600d822d20b0e5a0ca613935b1dfa4be838fcec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252827"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Habilite a criptografia de disco para nós de cluster de malha do Azure Service no Linux 
> [!div class="op_single_selector"]
> * [Criptografia de Disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Criptografia de Disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Neste tutorial, você aprenderá como ativar a criptografia de disco nos nós de cluster do Azure Service Fabric no Linux. Você precisará seguir essas etapas para cada um dos tipos de nó e conjuntos de escala de máquinavirtual. Para criptografar os nós, usaremos o recurso de criptografia de disco do Azure em conjuntos de escala de máquinas virtuais.

O guia abrange os seguintes tópicos:

* Conceitos-chave a serem cientes ao habilitar a criptografia de disco no service fabric cluster conjuntos de máquinas virtuais no Linux.
* Passos a serem seguidos antes de ativar a criptografia de disco nos nós de cluster service fabric no Linux.
* Passos a serem seguidos para habilitar a criptografia de disco nos nós de cluster service fabric no Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

 **Auto-registro**

A visualização de criptografia de disco para o conjunto de escala seleção de máquina virtual requer auto-registro. Use as seguintes etapas:

1. Execute o comando a seguir: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Aguarde cerca de 10 minutos até que o status leia *Registrado*. Você pode verificar o status executando o seguinte comando:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Cofre de Chaves Azure**

1. Crie um cofre de chaves na mesma assinatura e região como conjunto de dimensionamento. Em seguida, selecione a diretiva de acesso **EnabledForDiskEncryption** no cofre de chaves usando seu cmdlet PowerShell. Você também pode definir a política usando a ui do Key Vault no portal Azure com o seguinte comando:
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
* Tanto a criptografia quanto a criptografia desativada são suportadas para sO e volumes de dados em conjuntos de escala de máquinavirtual no Linux. 
* As operações de reimagem e upgrade da máquina virtual (VM) para conjuntos de escala de máquinas virtuais não são suportadas na visualização atual.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Crie um novo cluster e habilite a criptografia de disco

Use os seguintes comandos para criar um cluster e habilitar a criptografia de disco usando um modelo do Azure Resource Manager e um certificado auto-assinado.

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

Se você precisar criar um modelo personalizado, recomendamos que você use um dos modelos na página amostras de [modelo de criação de cluster do Azure Service Fabric.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) 

Se você já tiver um modelo personalizado, verifique duas vezes se todos os três parâmetros relacionados ao certificado no modelo e no arquivo de parâmetros são nomeados da seguinte forma. Certifique-se também de que os valores são nulos da seguinte forma:

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

Como apenas a criptografia de disco de dados é suportada para conjuntos de escala de máquinavirtual no Linux, você deve adicionar um disco de dados usando um modelo do Gerenciador de recursos. Atualize seu modelo para o provisionamento de discos de dados da seguinte forma:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
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

Aqui está o comando CLI equivalente. Alterar os valores nas declarações declaradas para os valores apropriados. O CLI suporta todos os outros parâmetros que o comando PowerShell anterior suporta.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Monte um disco de dados em uma instância Linux
Antes de continuar com a criptografia em um conjunto de escala de máquina virtual, certifique-se de que o disco de dados adicionado situou corretamente. Faça login no cluster Linux VM e execute o comando **LSBLK.** A saída deve mostrar que o disco de dados adicionado na coluna **Mount Point.**


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Implantar aplicativo para um cluster de malha de serviço no Linux
Para implantar um aplicativo no seu cluster, siga as etapas e orientações no [Quickstart: Implante contêineres Linux para service fabric](service-fabric-quickstart-containers-linux.md).


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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Valide se a criptografia de disco está habilitada para uma escala de máquina virtual definida no Linux
Para obter o status de um conjunto de escala de máquina virtual inteiro ou qualquer instância em um conjunto de escalas, execute os seguintes comandos.
Além disso, você pode entrar no cluster Linux VM e executar o comando **LSBLK.** A saída deve mostrar o disco de dados adicionado na coluna **Ponto de Montagem** e a coluna **Tipo** deve ler *Criptografia*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

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
Neste ponto, você deve ter um cluster seguro e saber como ativar e desativar a criptografia de disco para os nomes de cluster service fabric e conjuntos de escala de máquina virtual. Para obter orientações semelhantes sobre nós de cluster service fabric no Linux, consulte [Criptografia de disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
