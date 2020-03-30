---
title: Migrar para gerenciador de recursos com powershell
description: Este artigo percorre a migração suportada pela plataforma de recursos IaaS, como máquinas virtuais (VMs), redes virtuais e contas de armazenamento de clássicos para O Gerenciador de Recursos do Azure usando comandos Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 12a77c4c21a26f1ec52bb3ffdc312df56d3c4537
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249965"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migre os recursos do IaaS do classic para o Azure Resource Manager usando o PowerShell

> [!IMPORTANT]
> Hoje, cerca de 90% das VMs IaaS estão usando [o Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). A partir de 28 de fevereiro de 2020, as VMs clássicas foram preteridas e serão totalmente aposentadas em 1º de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre essa depreciação e [como ela afeta você](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Estas etapas mostram como usar os comandos do Azure PowerShell para migrar os recursos de IaaS (infraestrutura como serviço) do modelo de implantação clássico para o Modelo de implantação do Azure Resource Manager.

Se você quiser, você também pode migrar recursos usando o [Azure CLI](../linux/migration-classic-resource-manager-cli.md).

* Para obter informações sobre cenários de migração com suporte, confira [Migração de recursos de IaaS com suporte da plataforma do clássico ao Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Para obter orientação e um passo a passo sobre a migração, confira [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico ao Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Revise os erros de migração mais comuns](migration-classic-resource-manager-errors.md).

<br>
Aqui está um fluxograma para identificar a ordem na qual as etapas precisam ser executadas durante um processo de migração.

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Etapa 1: Planejar a migração
Aqui estão algumas práticas recomendadas que recomendamos ao avaliar se migrar recursos de IaaS de clássicos para Gerenciador de Recursos:

* Leia os [recursos e configurações com e sem suporte](migration-classic-resource-manager-overview.md). Se você tiver máquinas virtuais que usam configurações ou recursos não suportados, aguarde que a configuração ou o suporte a recursos sejam anunciados. Como alternativa, se isso atender às suas necessidades, remova esse recurso ou mude a configuração para habilitar a migração.
* Se você tiver scripts automatizados que implantam sua infraestrutura e aplicativos atualmente, tente criar uma configuração de teste semelhante usando esses scripts para migração. Você também pode configurar ambientes de exemplo usando o portal do Azure.

> [!IMPORTANT]
> Os gateways de aplicativos não são suportados no momento para migração do classic para o Resource Manager. Para migrar uma rede virtual com um gateway de aplicativo, remova o gateway antes de executar uma operação Prepare-se para mover a rede. Depois de concluir a migração, reconecte o gateway no Azure Resource Manager.
>
> Os gateways Do Azure ExpressRoute que se conectam aos circuitos expressroute em outra assinatura não podem ser migrados automaticamente. Nesses casos, remova o gateway ExpressRoute, migre a rede virtual e recrie o gateway. Para obter mais informações, consulte [os circuitos Migrar ExpressRoute e as redes virtuais associadas do modelo clássico para o modelo de implantação do Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="step-2-install-the-latest-version-of-powershell"></a>Passo 2: Instale a versão mais recente do PowerShell
Há duas opções principais para instalar o Azure PowerShell, a [Galeria do PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) e o [WebPI (Web Platform Installer)](https://aka.ms/webpi-azps). WebPI recebe atualizações mensais. A Galeria do PowerShell receberá atualizações continuamente. Este artigo tem base no Azure PowerShell versão 2.1.0.

Para obter instruções de instalação, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Passo 3: Certifique-se de que você é um administrador para a assinatura
Para realizar essa migração, você deve ser adicionado como um coadministrador para a assinatura no [portal Azure](https://portal.azure.com).

1. Faça login no [portal Azure](https://portal.azure.com).
2. No menu **Hub,** selecione **Assinatura**. Caso não visualize essa opção, selecione **Todos os serviços**.
3. Encontre a entrada de assinatura apropriada e, em seguida, olhe para o campo **MY ROLE.** Para um coadministrador, o valor deve ser _administrador de conta_.

Se você não for capaz de adicionar um coadministrador, entre em contato com um administrador de serviços ou coadministrador para que a assinatura seja adicionada.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Passo 4: Defina sua assinatura e inscreva-se para migração
Primeiro, inicie um prompt do PowerShell. Para migração, configure seu ambiente para o Classic e o Resource Manager.

Entre em sua conta para o modelo do Gerenciador de Recursos.

```powershell
    Connect-AzAccount
```

Obtenha as assinaturas disponíveis usando o comando a seguir:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Defina sua assinatura do Azure para a sessão atual. Este exemplo define o nome da assinatura padrão como **Minha Assinatura do Azure**. Substitua o nome da assinatura de exemplo pelo nome da sua própria assinatura.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> O registro é uma etapa única, mas você deve fazê-lo uma vez antes de tentar a migração. Sem o registro, você verá a seguinte mensagem de erro:
>
> *BadRequest: a assinatura não está registrada para migração.*

Registre-se no provedor de recursos de migração usando o comando a seguir:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Aguarde cinco minutos para que o registro termine. Verifique o status da aprovação usando o seguinte comando:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Verifique se RegistrationState é `Registered` antes de continuar.

Antes de mudar para o modelo clássico de implantação, certifique-se de que você tenha vCPUs de máquina virtual azure Resource Manager suficientes na região Azure de sua implantação atual ou rede virtual. Você pode usar o seguinte comando do PowerShell para verificar a quantidade atual de vCPUs no Azure Resource Manager. Para saber mais sobre cotas de vCPUs, veja [Limites e o Azure Resource Manager](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

Este exemplo verifica a disponibilidade na região **Oeste dos EUA**. Substitua o nome da região de exemplo pelo nome da sua própria região.

```powershell
    Get-AzVMUsage -Location "West US"
```

Agora, faça login na sua conta para o modelo clássico de implantação.

```powershell
    Add-AzureAccount
```

Obtenha as assinaturas disponíveis usando o comando a seguir:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Defina sua assinatura do Azure para a sessão atual. Este exemplo define a assinatura padrão como **Minha Assinatura do Azure**. Substitua o nome da assinatura de exemplo pelo nome da sua própria assinatura.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Etapa 5: Executar comandos para migrar os recursos de IaaS
* [Migrar VMs em um serviço de nuvem (não em uma rede virtual)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrar VMs em uma rede virtual](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrar uma conta de armazenamento](#step-52-migrate-a-storage-account)

> [!NOTE]
> Todas as operações descritas aqui são idempotentes. Caso você tenha algum problema que não seja um recurso sem suporte ou um erro de configuração, recomendamos que repita a operação de preparação, anulação ou confirmação. Em seguida, a plataforma tentará novamente a ação.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Passo 5.1: Opção 1 - Migrar máquinas virtuais em um serviço de nuvem (não em uma rede virtual)
Obtenha a lista de serviços em nuvem usando o seguinte comando. Em seguida, escolha o serviço de nuvem que você deseja migrar. Se as VMs no serviço de nuvem estiverem em uma rede virtual, ou se tiverem funções Web ou de trabalho, o comando retornará uma mensagem de erro.

```powershell
    Get-AzureService | ft Servicename
```

Obtenha o nome da implantação do serviço de nuvem. Neste exemplo, o nome do serviço é **Meu Serviço**. Substitua o nome do serviço de exemplo pelo nome de seu próprio serviço.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Prepare as máquinas virtuais no serviço de nuvem para migração. Você tem duas opções entre as quais escolher.

* **Opção 1: Migrar as VMs para uma rede virtual criada pela plataforma.**

    Primeiro, valide se você pode migrar o serviço em nuvem usando os seguintes comandos:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    O comando a seguir exibe todos os avisos e erros que bloqueiam a migração. Se a validação for bem sucedida, você pode passar para a etapa Preparar.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Opção 2: Migrar para uma rede virtual existente no modelo de implantação do Gerenciador de recursos.**

    Este exemplo define o nome do grupo de recursos para **myResourceGroup**, o nome da rede virtual para **myVirtualNetwork**e o nome da sub-rede para **mySubNet**. Substitua os nomes de exemplo pelos nomes de seus próprios recursos.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Primeiro, valide se você pode migrar a rede virtual usando o seguinte comando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    O comando a seguir exibe todos os avisos e erros que bloqueiam a migração. Se a validação for bem sucedida, você pode prosseguir com a seguinte etapa de Preparação:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Após a operação de Preparação ser bem-sucedida com uma das opções anteriores, consulte o estado de migração das VMs. Certifique-se de que `Prepared` eles estão no estado.

Este exemplo define o nome da VM como **myVM**. Substitua o nome de exemplo pelo nome de sua própria VM.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Verifique a configuração dos recursos preparados usando o PowerShell ou o Portal do Azure. Se você não está pronto para migração e deseja voltar para o estado antigo, use o seguinte comando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Passo 5.1: Opção 2 - Migrar máquinas virtuais em uma rede virtual

Para migrar máquinas virtuais em uma rede virtual, migre a rede virtual. As máquinas virtuais são migradas automaticamente com a rede virtual. Selecione a rede virtual que você deseja migrar.
> [!NOTE]
> [Migrar uma única máquina virtual](migrate-single-classic-to-resource-manager.md) criada usando o modelo clássico de implantação criando uma nova máquina virtual Resource Manager com Discos Gerenciados usando os arquivos VHD (OS e dados) da máquina virtual.
<br>

> [!NOTE]
> O nome da rede virtual pode ser diferente do que é mostrado no novo portal. O novo portal Azure `[vnet-name]`exibe o nome como , `Group [resource-group-name] [vnet-name]`mas o nome real da rede virtual é do tipo . Antes de iniciar a migração, procure o nome `Get-AzureVnetSite | Select -Property Name` real da rede virtual usando o comando ou visualize-o no antigo portal Azure. 

Este exemplo define o nome de rede virtual como **myVnet**. Substitua o nome de exemplo pelo nome da sua própria rede virtual.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Se a rede virtual contiver funções Web ou de trabalho ou VMs com configurações sem suporte, você receberá uma mensagem de erro de validação.

Primeiro, valide se você pode migrar a rede virtual usando o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

O comando a seguir exibe todos os avisos e erros que bloqueiam a migração. Se a validação for bem sucedida, você pode prosseguir com a seguinte etapa de Preparação:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Verifique a configuração para as máquinas virtuais preparadas usando o Azure PowerShell ou o Portal do Azure. Se você não está pronto para migração e deseja voltar para o estado antigo, use o seguinte comando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>Passo 5.2: Migrar uma conta de armazenamento
Depois de terminar de migrar as máquinas virtuais, execute as seguintes verificações pré-requisitos antes de migrar as contas de armazenamento.

> [!NOTE]
> Se sua conta de armazenamento não tiver discos ou dados VM associados, você pode pular diretamente para a seção "Validar contas de armazenamento e iniciar a migração".

* Verificações pré-requisitos se você migrou quaisquer VMs ou sua conta de armazenamento tem recursos de disco:
    * Migrar máquinas virtuais cujos discos são armazenados na conta de armazenamento.

        O comando a seguir retorna as propriedades RoleName e DiskName de todos os discos VM na conta de armazenamento. RoleName é o nome da máquina virtual à qual um disco está anexado. Se esse comando retornar discos, certifique-se de que as máquinas virtuais às quais esses discos estão conectados sejam migradas antes de migrar a conta de armazenamento.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Exclua discos VM não conectados armazenados na conta de armazenamento.

        Encontre discos VM não conectados na conta de armazenamento usando o seguinte comando:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Se o comando anterior retornar discos, exclua esses discos usando o seguinte comando:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Exclua imagens VM armazenadas na conta de armazenamento.

        O comando a seguir retorna todas as imagens VM com discos do SISTEMA OPERACIONAL armazenados na conta de armazenamento.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         O comando a seguir retorna todas as imagens de VM com discos de dados armazenados na conta de armazenamento.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Exclua todas as imagens VM retornadas pelos comandos anteriores usando este comando:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Valide contas de armazenamento e inicie a migração.

    Valide cada conta de armazenamento para migração usando o comando a seguir. Neste exemplo, o nome da conta de armazenamento é **myStorageAccount**. Substitua o nome de exemplo pelo nome da sua própria conta de armazenamento.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    O próximo passo é preparar a conta de armazenamento para migração.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Verifique a configuração da conta de armazenamento preparada usando o Azure PowerShell ou o Portal do Azure. Se você não está pronto para migração e deseja voltar para o estado antigo, use o seguinte comando:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Se a configuração preparada estiver correta, será possível continuar e confirmar os recursos usando o comando a seguir:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Próximas etapas
* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planejamento da migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Ferramentas da comunidade para ajudar com a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Examinar os erros de migração mais comuns](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Confira as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
