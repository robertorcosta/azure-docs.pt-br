---
title: Reparos automáticos de instâncias com conjuntos de escala de máquinavirtual do Azure
description: Saiba como configurar a política de reparos automáticos para instâncias de VM em um conjunto de escalas
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: 8156c563573183e51e06650914117f8787922e93
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603675"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Reparos automáticos de instâncias para conjuntos de escala de máquinas virtuais do Azure

Permitir reparos automáticos de instâncias para conjuntos de escalade máquinas virtuais do Azure ajuda a obter alta disponibilidade para aplicativos, mantendo um conjunto de instâncias saudáveis. Se uma instância no conjunto de escalas for considerada insalubre, conforme relatado pelos testes de [saúde da extensão Application Health](./virtual-machine-scale-sets-health-extension.md) ou do [Balanceador de carga,](../load-balancer/load-balancer-custom-probe-overview.md)esse recurso executa automaticamente o reparo da instância excluindo a instância insalubre e criando uma nova para substituí-la.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Requisitos para o uso de reparos automáticos de instâncias

**Habilite o monitoramento de saúde do aplicativo para o conjunto de escalas**

O conjunto de escalas deve ter monitoramento de saúde de aplicação para os casos habilitados. Isso pode ser feito usando a [extensão application health](./virtual-machine-scale-sets-health-extension.md) ou [testes de saúde do balanceador load balancer](../load-balancer/load-balancer-custom-probe-overview.md). Apenas um destes pode ser habilitado de cada vez. A extensão de saúde do aplicativo ou o balanceador de carga testa o ping do ponto final do aplicativo configurado em instâncias de máquinas virtuais para determinar o estado de saúde do aplicativo. Esse estado de saúde é utilizado pelo orquestrador de conjuntos de escalapara monitorar a saúde da ocorrência e realizar reparos quando necessário.

**Configurar ponto final para fornecer status de saúde**

Antes de habilitar a política de reparoautomático de instâncias, certifique-se de que as instâncias de conjunto de escala tenham o ponto final do aplicativo configurado para emitir o status de saúde do aplicativo. Quando uma instância retorna o status 200 (OK) neste ponto final do aplicativo, então a instância é marcada como "Saudável". Em todos os outros casos, a ocorrência está marcada como "Insalubre", incluindo os seguintes cenários:

- Quando não há um ponto final de aplicativo configurado dentro das instâncias da máquina virtual para fornecer o status de saúde do aplicativo
- Quando o ponto final do aplicativo estiver configurado incorretamente
- Quando o ponto final do aplicativo não é acessível

Para casos marcados como "Insalubres", os reparos automáticos são acionados pelo conjunto de escalas. Certifique-se de que o ponto final do aplicativo está configurado corretamente antes de ativar a política de reparos automáticos para evitar reparos de instâncias não intencionais, enquanto o ponto final está sendo configurado.

**Habilitar um único grupo de colocação**

Este recurso está atualmente disponível apenas para conjuntos de escala implantados como um único grupo de colocação. O *singlePlacementGroup* de propriedade deve ser definido *como verdadeiro* para o seu conjunto de escala sustapara usar o recurso de reparos automáticos de instâncias. Saiba mais sobre [grupos de colocação](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**Versão aPI**

A política de reparos automáticos é suportada para a versão aPI de computação 2018-10-01 ou superior.

**Restrições a recursos ou movimentos de assinatura**

Atualmente, os movimentos de recursos ou de assinatura não são suportados para conjuntos de escala quando o recurso de reparos automáticos está ativado.

**Restrição para conjuntos de escala de malha de serviço**

No momento, esse recurso não é suportado para conjuntos de escala de malha de serviço.

## <a name="how-do-automatic-instance-repairs-work"></a>Como funcionam os reparos automáticos de instâncias?

O recurso de reparo automático de instâncias depende do monitoramento de saúde de instâncias individuais em um conjunto de escalas. As instâncias de VM em um conjunto de escalas podem ser configuradas para emitir o status de saúde do aplicativo usando a [extensão Application Health](./virtual-machine-scale-sets-health-extension.md) ou [os testes de saúde do balanceador load .](../load-balancer/load-balancer-custom-probe-overview.md) Se uma instância for considerada insalubre, o conjunto de escalas executa a ação de reparo excluindo a instância insalubre e criando uma nova para substituí-la. O modelo mais recente de conjunto de escala de máquina virtual é usado para criar a nova instância. Esse recurso pode ser habilitado no modelo de conjunto de escalade máquina virtual usando o objeto *AutomaticRepairsPolicy.*

### <a name="batching"></a>Envio em lote

As operações automáticas de reparo de instâncias são realizadas em lotes. A qualquer momento, não mais de 5% das instâncias no conjunto de escalas são reparadas através da política de reparos automáticos. Isso ajuda a evitar a exclusão simultânea e a recriação de um grande número de casos se considerados insalubres ao mesmo tempo.

### <a name="grace-period"></a>Período de carência

Quando uma instância passa por uma operação de alteração de estado por causa de uma ação PUT, PATCH ou POST realizada no conjunto de escala (por exemplo, reimagem, reimplantação, atualização, etc.), então qualquer ação de reparo nessa instância é executada somente após a espera pelo período de carência. O período de carência é a quantidade de tempo para permitir que a instância retorne ao estado saudável. O período de carência começa após a mudança de estado ter sido concluída. Isso ajuda a evitar qualquer operação de reparo prematura ou acidental. O período de carência é honrado por qualquer instância recém-criada no conjunto de escalas (incluindo a criada como resultado da operação de reparo). O período de carência é especificado em minutos no formato ISO 8601 e pode ser definido usando a propriedade *automáticaRepairsPolicy.gracePeriod*. O período de carência pode variar entre 30 minutos e 90 minutos, e tem um valor padrão de 30 minutos.

### <a name="suspension-of-repairs"></a>Suspensão de Reparos 

Os conjuntos de escala de máquinas virtuais fornecem a capacidade de suspender temporariamente os reparos automáticos de instância, se necessário. O *serviceState* para reparos automáticos sob a orquestração de *propriedadesServiços* em exibição de exemplo do conjunto de escala seleto de máquinavirtual mostra o estado atual dos reparos automáticos. Quando um conjunto de escalaé optado por reparos automáticos, o valor do serviço de *parâmetroSEstado* é definido como *Execução*. Quando os reparos automáticos são suspensos para um conjunto de escalas, o parâmetro *serviceState* é definido *como Suspenso*. Se *AutomaticRepairsPolicy* for definido em um conjunto de escalas, mas o recurso de reparos automáticos não estiver ativado, então o *modo de serviço de parâmetroSEstado* está definido como Não Em *Execução*.

Se os casos recém-criados para substituir os insalubres em um conjunto de escala continuam a permanecer insalubres mesmo após a realização repetida de operações de reparo, então como medida de segurança a plataforma atualiza o *serviceState* para reparos automáticos para *Suspensos*. Você pode retomar os reparos automáticos definindo o valor do *serviceState* para reparos automáticos *em Execução*. Instruções detalhadas são fornecidas na seção sobre [visualização e atualização do estado de serviço da política de reparos automáticos](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) para o seu conjunto de escalas. 

O processo automático de reparos de instâncias funciona da seguinte forma:

1. [A extensão de saúde do aplicativo](./virtual-machine-scale-sets-health-extension.md) ou o [balanceador de carga testa](../load-balancer/load-balancer-custom-probe-overview.md) o ponto final do aplicativo dentro de cada máquina virtual na escala definida para obter o status de saúde do aplicativo para cada instância.
2. Se o ponto final responder com um status 200 (OK), então a instância será marcada como "Saudável". Em todos os outros casos (inclusive se o ponto final for inalcançável), a ocorrência está marcada como "Insalubre".
3. Quando uma instância é considerada insalubre, o conjunto de escala susta uma ação de reparo excluindo a instância insalubre e criando uma nova para substituí-la.
4. Os reparos de instâncias são realizados em lotes. A qualquer momento, não mais de 5% do total de instâncias no conjunto de escalas são reparadas. Se um conjunto de escala tiver menos de 20 instâncias, os reparos são feitos para uma instância insalubre de cada vez.
5. O processo acima continua até que todas as instâncias insalubres no conjunto de escala sejam reparadas.

## <a name="instance-protection-and-automatic-repairs"></a>Proteção de instâncias e reparos automáticos

Se uma instância em um conjunto de escala for protegida aplicando uma das políticas de [proteção,](./virtual-machine-scale-sets-instance-protection.md)os reparos automáticos não serão realizados nessa instância. Isso se aplica a ambas as políticas de proteção: *Proteja-se contra escala-in* e *proteja-se contra* ações definidas em escala. 

## <a name="terminatenotificationandautomaticrepairs"></a>Encerrar notificação e reparos automáticos

Se o recurso [de notificação de término](./virtual-machine-scale-sets-terminate-notification.md) estiver habilitado em um conjunto de escalas, então durante a operação de reparo automático, a exclusão de uma instância insalubre seguirá a configuração de notificação de término. Uma notificação de término é enviada através do serviço de metadados do Azure – eventos agendados – e a exclusão de instâncias é adiada durante a duração do tempo de atraso configurado. No entanto, a criação de uma nova instância para substituir o insalubre não espera que o tempo de atraso seja concluído.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Habilitar a política de reparos automáticos ao criar um novo conjunto de escalas

Para habilitar a política de reparos automáticos ao criar um novo conjunto de escalas, certifique-se de que todos os [requisitos](#requirements-for-using-automatic-instance-repairs) para optar por esse recurso sejam atendidos. O ponto final do aplicativo deve ser configurado corretamente para instâncias de conjunto de escala para evitar o acionamento de reparos não intencionais enquanto o ponto final estiver sendo configurado. Para conjuntos de escala recém-criados, qualquer reparo de instância é realizado somente após a espera do período de carência. Para habilitar o reparo automático de instâncias em um conjunto de escalas, use o objeto *AutomaticRepairsPolicy* no modelo de conjunto de escalas de máquina virtual.

Você também pode usar este [modelo quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) para implantar um conjunto de escala de máquina virtual com teste de saúde do balanceador de carga e reparos automáticos de instâncias habilitados com um período de carência de 30 minutos.

### <a name="azure-portal"></a>Portal do Azure
 
As etapas a seguir que permitem a política de reparos automáticos ao criar um novo conjunto de escalas.
 
1. Vá para **conjuntos de escala de máquinas virtuais**.
1. Selecione **+ Adicione** para criar um novo conjunto de escalas.
1. Vá para a aba **Saúde.** 
1. Localize a seção **de Saúde.**
1. Habilite a opção **de saúde do aplicativo Monitor.**
1. Localize a seção **de política de reparo automática.**
1. **Ligue** a opção **de reparos automáticos.**
1. No **período de carência (min),** especifique o período de carência em minutos, os valores permitidos são entre 30 e 90 minutos. 
1. Quando terminar de criar o novo conjunto de escalas, selecione **'Revisar + criar'.**

### <a name="rest-api"></a>API REST

O exemplo a seguir mostra como ativar o reparo automático de instâncias em um modelo de conjunto de escalas. Use a versão aPI 2018-10-01 ou superior.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

O recurso de reparo automático de instâncias pode ser ativado ao criar um novo conjunto de escalausando o cmdlet [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig) Este script de exemplo percorre a criação de um conjunto de escalas e recursos associados usando o arquivo de configuração: [Crie um conjunto completo de escala de máquina virtual](./scripts/powershell-sample-create-complete-scale-set.md). Você pode configurar a política de reparos automáticos de instâncias adicionando os parâmetros *EnableAutomaticRepair* e *AutomaticRepairGracePeriod* ao objeto de configuração para criar o conjunto de escalas. O exemplo a seguir permite o recurso com um período de carência de 30 minutos.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0

O exemplo a seguir permite a política de reparos automáticos ao criar um novo conjunto de escalas usando *[acriação de az vmss](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)*. Primeiro crie um grupo de recursos e crie um novo conjunto de escalas com o período de carência da política de reparos automáticos definido para 30 minutos.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-period 30
```

O exemplo acima usa um balanceador de carga existente e um teste de saúde para monitorar o estado de saúde das aplicações de instâncias. Se você preferir usar uma extensão de saúde de aplicativo para monitoramento, você pode criar um conjunto de escalas, configurar a extensão de saúde do aplicativo e, em seguida, ativar a política de reparos automáticos de instâncias usando a *atualização az vmss*, como explicado na próxima seção.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Habilitar a política de reparos automáticos ao atualizar um conjunto de escalas existente

Antes de habilitar a política de reparos automáticos em um conjunto de escalas existente, certifique-se de que todos os [requisitos](#requirements-for-using-automatic-instance-repairs) para optar por esse recurso sejam atendidos. O ponto final do aplicativo deve ser configurado corretamente para instâncias de conjunto de escala para evitar o acionamento de reparos não intencionais enquanto o ponto final estiver sendo configurado. Para habilitar o reparo automático de instâncias em um conjunto de escalas, use o objeto *AutomaticRepairsPolicy* no modelo de conjunto de escalas de máquina virtual.

Após atualizar o modelo de um conjunto de escalas existente, certifique-se de que o modelo mais recente seja aplicado a todas as instâncias da escala. Consulte a instrução sobre [como colocar as VMs atualizadas com o modelo de conjunto de escala supérdia](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-portal"></a>Portal do Azure

Você pode modificar a política de reparos automáticos de uma escala existente definida através do portal Azure. 
 
1. Vá para um conjunto de escala de máquina virtual existente.
1. Em **Configurações** no menu à esquerda, selecione **Saúde e reparo**.
1. Habilite a opção **de saúde do aplicativo Monitor.**
1. Localize a seção **de política de reparo automática.**
1. **Ligue** a opção **de reparos automáticos.**
1. No **período de carência (min),** especifique o período de carência em minutos, os valores permitidos são entre 30 e 90 minutos. 
1. Quando terminar, selecione **Salvar**. 

### <a name="rest-api"></a>API REST

O exemplo a seguir permite a apólice com período de carência de 40 minutos. Use a versão aPI 2018-10-01 ou superior.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Use o [cmdlet Update-AzVmss](/powershell/module/az.compute/update-azvmss) para modificar a configuração do recurso de reparo automático de instâncias em um conjunto de escalas existente. O exemplo a seguir atualiza o período de carência para 40 minutos.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>CLI do Azure 2.0

O exemplo a seguir é um exemplo para atualizar a política de reparos automáticos de instâncias de um conjunto de escala existente, usando *[a atualização az vmss](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Visualização e atualização do estado de serviço da política de reparos automáticos de instância

### <a name="rest-api"></a>API REST 

Use [obter exibição de instância](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview) com a versão da API 2019-12-01 ou superior para a escala de máquina virtual definida para exibir o *serviceState* para reparos automáticos sob a *orquestração de propriedadesServices*. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Use *setOrchestrationServiceState* API com a versão API 2019-12-01 ou superior em uma escala de máquina virtual definida para definir o estado dos reparos automáticos. Uma vez que o conjunto de escalas seja optado pelo recurso de reparos automáticos, você pode usar esta API para suspender ou retomar reparos automáticos para o seu conjunto de escalas. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>CLI do Azure 

Use [cmdlet de visualização de instância](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view) para exibir o *serviceState* para reparos automáticos de instâncias. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Use [o cmdlet set-orchestration-service-state](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state) para atualizar o *serviceState* para reparos automáticos de instâncias. Uma vez que o conjunto de escalaé optado pelo recurso de reparo automático, então você pode usar este cmdlet para suspender ou retomar reparos automáticos para o conjunto de escalas. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Use [o cmdlet Get-AzVms](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) com parâmetro *InstanceView* para exibir o *ServiceState* para reparos automáticos de instâncias.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Use Set-AzVmsSOrchestrationServiceServiceState cmdlet para atualizar o *serviceState* para reparos automáticos de instâncias. Uma vez que o conjunto de escalaé optado pelo recurso de reparo automático, você pode usar este cmdlet para suspender ou retomar reparos automáticos para o conjunto de escalas.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Solucionar problemas

**Falha em habilitar a política de reparos automáticos**

Se você receber um erro 'BadRequest' com uma mensagem informando "Não foi possível encontrar membro 'AutomaticRepairsPolicy' em objeto de 'propriedades' do tipo", verifique a versão da API usada para o conjunto de escala da máquina virtual. A versão aPI 2018-10-01 ou superior é necessária para este recurso.

**Exemplo de não ser reparado mesmo quando a política está ativada**

A instância pode estar em período de carência. Este é o tempo de espera após qualquer alteração de estado na instância antes de realizar os reparos. Isto é para evitar quaisquer reparos prematuros ou acidentais. A ação de reparo deve acontecer assim que o período de carência for concluído para a ocorrência.

**Visualização do status de saúde do aplicativo para instâncias definidas em escala**

Você pode usar a [API Get Instance View](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) para exemplos em uma escala de máquina virtual definida para visualizar o estado de saúde do aplicativo. Com o Azure PowerShell, você pode usar o cmdlet [Get-AzVmsVM](/powershell/module/az.compute/get-azvmssvm) com o sinalizador *-InstanceView.* O estado de saúde do aplicativo é fornecido sob a propriedade *vmHealth*.

No portal Azure, você pode ver o estado de saúde também. Vá para um conjunto de escalas existente, selecione **Instâncias** do menu à esquerda e veja a coluna Estado de **Saúde** para saber o estado de saúde de cada instância definida de escala. 

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar os testes de [saúde do application health](./virtual-machine-scale-sets-health-extension.md) ou load balancer para seus [conjuntos](../load-balancer/load-balancer-custom-probe-overview.md) de escala.
