---
title: Controle de manutenção para máquinas virtuais Azure usando PowerShell
description: Saiba como controlar quando a manutenção é aplicada às Suas VMs Azure usando controle de manutenção e PowerShell.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: dc47afe9cb6eca1b10f8caca7b85087023c5eadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060128"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Visualização: Atualizações de controle com controle de manutenção e PowerShell do Azure

Gerenciar atualizações de plataforma, que não requerem uma reinicialização, usando o controle de manutenção. O Azure atualiza frequentemente sua infra-estrutura para melhorar a confiabilidade, o desempenho, a segurança ou lançar novos recursos. A maioria das atualizações são transparentes para os usuários. Algumas cargas de trabalho sensíveis, como jogos, streaming de mídia e transações financeiras, não podem tolerar nem mesmo alguns segundos de uma VM congelando ou desconectando para manutenção. O controle de manutenção lhe dá a opção de esperar pelas atualizações da plataforma e aplicá-las dentro de uma janela de rolamento de 35 dias. 

O controle de manutenção permite que você decida quando aplicar atualizações às suas VMs isoladas.

Com o controle de manutenção, você pode:
- Atualizações em lote em um pacote de atualização.
- Aguarde até 35 dias para aplicar atualizações. 
- Automatize as atualizações da plataforma para sua janela de manutenção usando funções do Azure.
- As configurações de manutenção funcionam entre assinaturas e grupos de recursos. 

> [!IMPORTANT]
> O Controle de Manutenção está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Limitações

- As VMs devem estar em um [host dedicado](./linux/dedicated-hosts.md)ou serem criadas usando um tamanho [de VM isolado](./linux/isolation.md).
- Após 35 dias, uma atualização será aplicada automaticamente.
- O usuário deve ter acesso **ao Contribuinte de Recursos.**


## <a name="enable-the-powershell-module"></a>Habilite o módulo PowerShell

Certifique-se de que `PowerShellGet` está atualizado.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Os cmdlets Az.Maintenance PowerShell estão em pré-visualização, `AllowPrerelease` então você precisa instalar o módulo com o parâmetro na Cloud Shell ou na instalação local do PowerShell.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Se você estiver instalando localmente, certifique-se de abrir seu prompt PowerShell como administrador.

Você também pode ser solicitado a confirmar que deseja instalar a partir de um *repositório não confiável*. Digite `Y` ou selecione **Sim para Todos** para instalar o módulo.



## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

Crie um grupo de recursos como um contêiner para sua configuração. Neste exemplo, um grupo de recursos chamado *myMaintenanceRG* é criado no *eastus*. Se você já tem um grupo de recursos que deseja usar, você pode pular essa parte e substituir o nome do grupo de recursos por você próprio no resto dos exemplos.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Use [new-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) para criar uma configuração de manutenção. Este exemplo cria uma configuração de manutenção chamada *myConfig* escopo para o host. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

O `-MaintenanceScope host` uso garante que a configuração de manutenção seja usada para controlar as atualizações do host.

Se você tentar criar uma configuração com o mesmo nome, mas em um local diferente, você terá um erro. Os nomes de configuração devem ser exclusivos da sua assinatura.

Você pode consultar as configurações de manutenção disponíveis usando [Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>Atribuir a configuração

Use [New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) para atribuir a configuração à sua VM isolada ou ao Host dedicado do Azure.

### <a name="isolated-vm"></a>VM isolado

Aplique a configuração em um VM usando o ID da configuração. Especificar `-ResourceType VirtualMachines` e fornecer o nome `-ResourceName`da VM para , `-ResourceGroupName`e o grupo de recursos da VM para . 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>Host dedicado

Para aplicar uma configuração a um host `-ResourceType hosts`dedicado, você também precisa incluir , `-ResourceParentName` com o nome do grupo host, e `-ResourceParentType hostGroups`. 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Verifique se há atualizações pendentes

Use [Get-AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) para ver se há atualizações pendentes. Use `-subscription` para especificar a assinatura do Azure da VM se ela for diferente da que você está logado.

Se não houver atualizações para mostrar, este comando não retornará nada. Caso contrário, ele retornará um objeto PSApplyUpdate:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>VM isolado

Verifique se há atualizações pendentes para uma VM isolada. Neste exemplo, a saída é formatada como uma tabela de legibilidade.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Host dedicado

Para verificar se há atualizações pendentes para um host dedicado. Neste exemplo, a saída é formatada como uma tabela de legibilidade. Substitua os valores dos recursos pelos seus.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Aplicar atualizações

Use [new-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) para aplicar atualizações pendentes.

### <a name="isolated-vm"></a>VM isolado

Crie uma solicitação para aplicar atualizações a uma VM isolada.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

Com o sucesso, este `PSApplyUpdate` comando retornará um objeto. Você pode usar o `Get-AzApplyUpdate` atributo Nome no comando para verificar o status da atualização. Consulte [Verificar o status da atualização](#check-update-status).

### <a name="dedicated-host"></a>Host dedicado

Aplique atualizações a um host dedicado.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Verifique o status da atualização
Use [Get-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) para verificar o status de uma atualização. Os comandos mostrados abaixo mostram o `default` status `-ApplyUpdateName` da última atualização usando para o parâmetro. Você pode substituir o nome da atualização (retornado pelo comando [New-AzApplyUpdate)](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) para obter o status de uma atualização específica.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime será o momento em que a atualização foi concluída, iniciada por você ou pela plataforma no caso de janela de auto-manutenção não ter sido usada. Se nunca houve uma atualização aplicada através do controle de manutenção, ela mostrará o valor padrão.

### <a name="isolated-vm"></a>VM isolado

Verifique se há atualizações para uma máquina virtual específica.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Host dedicado

Verifique se há atualizações em um host dedicado.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Remova uma configuração de manutenção

Use [remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) para excluir uma configuração de manutenção.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte [Manutenção e atualizações](maintenance-and-updates.md).
