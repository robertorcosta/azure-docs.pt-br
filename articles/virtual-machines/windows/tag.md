---
title: Como marcar um recurso de VM do Windows no Azure
description: Aprenda a marcar uma máquina virtual do Windows criada no Azure usando o modelo de implantação do Gerenciador de Recursos
author: mmccrory
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 84fc6e6fee0b4a85b909c9b43a17381b31f39abf
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291992"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Como marcar uma máquina virtual do Windows no Azure
Este artigo descreve as diferentes maneiras de marcar uma máquina virtual do Windows no Azure por meio do modelo de implantação do Resource Manager. As marcas são pares de chave/valor definidos pelo usuário que podem ser colocados diretamente em um recurso ou grupo de recursos. Atualmente, o Azure dá suporte a até 50 marcas por recurso e grupo de recursos. As marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente. Observe que as marcas tem suporte apenas para recursos criados por meio do modelo de implantação do Resource Manager. Se quiser marcar uma máquina virtual Linux, consulte [Como marcar uma máquina virtual Linux no Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Marcação com o PowerShell
Para criar, adicionar e excluir marcas pelo PowerShell, primeiramente, você precisa configurar o [ambiente do PowerShell com o Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Depois de concluir a instalação, você pode colocar marcas em recursos de Computação, Rede e Armazenamento na criação ou depois que o recurso for criado via PowerShell. Este artigo se concentra na exibição/edição de marcas colocadas nas Máquinas Virtuais.

 

Primeiramente, navegue para uma Máquina Virtual usando o cmdlet `Get-AzVM` .

```azurepowershell
PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"
```

Se sua Máquina Virtual já contiver marcas, você verá todas elas no seu recurso:

```json
Tags : {
        "Application": "MyApp1",
        "Created By": "MyName",
        "Department": "MyDepartment",
        "Environment": "Production"
        }
```

Se desejar adicionar marcas por meio do PowerShell, você poderá usar o comando `Set-AzResource` . Observe que, ao atualizar marcas pelo PowerShell, as marcas são atualizadas como um todo. Desse modo, se estiver adicionando uma marca a um recurso que já tenha marcas, você precisará incluir todas as marcas que deseja que sejam colocadas no recurso. Veja abaixo um exemplo de como adicionar mais marcas a um recurso usando cmdlets do PowerShell.

Este primeiro cmdlet define todas as marcações colocadas em *MyTestVM* para a variável *$tags* usando as propriedades `Get-AzResource` e `Tags`.

```azurepowershell
PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags
```

O segundo comando exibe as marcas para a variável fornecida.

```azurepowershell
PS C:\> $tags

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

O terceiro comando adiciona uma marca adicional à variável *$Tags* . Observe o uso do **+=** para acrescentar o novo par de chave/valor à lista de *$Tags* .

```azurepowershell
PS C:\> $tags += @{Location="MyLocation"}
```

O quarto comando define todas as marcas definidas na variável *$Tags* para o recurso fornecido. Nesse caso, é MyTestVM.

```azurepowershell
PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

O quinto comando exibe todas as marcas no recurso. Como você pode ver, *Location* agora está definido como uma marcação com *MyLocation* como valor.

```azurepowershell
PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

Para saber mais sobre marcação usando o PowerShell, confira [Cmdlets de recursos do Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como marcar seus recursos do Azure, consulte [Visão geral do Azure Resource Manager][Azure Resource Manager Overview] e [Usando marcas para organizar os Recursos do Azure][Using Tags to organize your Azure Resources].
* Para ver como as marcas podem ajudá-lo a gerenciar seu uso de recursos do Azure, consulte [Noções básicas de sua fatura do Azure][Understanding your Azure Bill] e [Obtenha informações sobre o consumo de recursos do Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/management/manage-resources-powershell.md
[Azure Resource Cmdlets]: /powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
