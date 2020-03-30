---
title: Mova recursos associados a uma configuração de manutenção para outra região
description: Saiba como mover recursos associados a uma configuração de manutenção de VM para outra região do Azure
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 3e271e2467b495e79a93ce5eab5edee36e65e619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304440"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Mova recursos em uma configuração de Controle de Manutenção para outra região

Siga este artigo para mover recursos associados a uma configuração de Controle de Manutenção para uma região diferente do Azure. Você pode querer mover uma configuração por uma série de razões. Por exemplo, aproveitar uma nova região, implantar recursos ou serviços disponíveis em uma região específica, atender aos requisitos internos de política e governança ou em resposta ao planejamento de capacidade.

O controle de manutenção, com configurações de manutenção personalizadas, permite controlar como as atualizações da plataforma são aplicadas às VMs [Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) e [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) e aos Hosts Dedicados do Azure. Existem alguns cenários para mover o controle de manutenção entre as regiões:

- Para mover os recursos associados a uma configuração de manutenção, mas não a configuração em si, siga este artigo.
- Para mover sua configuração de controle de manutenção, mas não os recursos associados à configuração, siga [estas instruções](move-region-maintenance-configuration.md).
- Para mover tanto a configuração de manutenção quanto os recursos associados a ela, primeiro siga [estas instruções](move-region-maintenance-configuration.md). Em seguida, siga as instruções deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a mover os recursos associados a uma configuração de controle de manutenção:

- Certifique-se de que os recursos que você está movendo existem na nova região antes de começar.
- Verifique as configurações de controle de manutenção associadas às VMs azure e aos hosts dedicados do Azure que você deseja mover. Verifique cada recurso individualmente. No momento, não há como recuperar configurações para vários recursos.
- Ao recuperar configurações de um recurso:
    - Certifique-se de usar o ID de assinatura para a conta, não um ID de host dedicado do Azure.
    - CLI: O parâmetro de tabela de saída é usado apenas para legibilidade e pode ser excluído ou alterado.
    - PowerShell: O parâmetro Nome da tabela de formato é usado apenas para legibilidade e pode ser excluído ou alterado.
    - Se você usar o PowerShell, você terá um erro se tentar listar configurações de um recurso que não tenha nenhuma configuração associada. O erro será semelhante ao: "Operação falhou com status: 'Não encontrado'. Detalhes: Erro do cliente 404: Não encontrado para url".

    
## <a name="prepare-to-move"></a>Prepare-se para se mover

1. Antes de começar, defina essas variáveis. Nós fornecemos um exemplo para cada um.

    **Variável** | **Detalhes** | **Exemplo**
    --- | ---
    $subId | ID para assinatura contendo as configurações de manutenção | "nossa assinatura-ID"
    $rsrcGroupName | Nome do grupo de recursos (Azure VM) | "VMResourceGroup"
    $vmName | Nome do recurso VM |  "myVM"
    $adhRsrcGroupName |  Grupo de recursos (hosts dedicados) | "HostResourceGroup"
    $adh | Nome de host dedicado | "myHost"
    $adhParentName | Nome do recurso dos pais | "HostGroup"
    
2. Para recuperar as configurações de manutenção usando o comando PowerShell [Get-AZConfigurationAssignment:](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0)

    - Para hosts dedicados do Azure, execute:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Para VMs azure, execute:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Para recuperar as configurações de manutenção usando o comando de atribuição de manutenção CLI [az:](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest)

    - Para hosts dedicados ao Azure:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Para VMs azure:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Mover 

1. [Siga estas instruções](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) para mover as VMs do Azure para a nova região.
2. Depois que os recursos forem movidos, reaplique as configurações de manutenção aos recursos da nova região conforme apropriado, dependendo se você mudou as configurações de manutenção. Você pode aplicar uma configuração de manutenção a um recurso usando [PowerShell](../virtual-machines/maintenance-control-powershell.md) ou [CLI](../virtual-machines/maintenance-control-cli.md).


## <a name="verify-the-move"></a>Verifique o movimento

Verifique os recursos na nova região e verifique as configurações associadas para os recursos na nova região. 

## <a name="clean-up-source-resources"></a>Limpar os recursos de origem

Após a mudança, considere excluir os recursos movimentados na região de origem.


## <a name="next-steps"></a>Próximas etapas

Siga [estas instruções](move-region-maintenance-configuration.md) se precisar mover as configurações de manutenção. 
