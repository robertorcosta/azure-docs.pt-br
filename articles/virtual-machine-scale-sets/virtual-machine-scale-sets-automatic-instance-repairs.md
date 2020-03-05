---
title: Reparos automáticos de instância com conjuntos de dimensionamento de máquinas virtuais do Azure
description: Saiba como configurar a política de reparos automáticos para instâncias de VM em um conjunto de dimensionamento
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274808"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Visualização: reparos de instância automática para conjuntos de dimensionamento de máquinas virtuais do Azure

A habilitação de reparos de instância automática para conjuntos de dimensionamento de máquinas virtuais do Azure ajuda a obter alta disponibilidade para aplicativos mantendo um conjunto de instâncias íntegras. Se uma instância no conjunto de dimensionamento for considerada não íntegra conforme relatado pela extensão de [integridade do aplicativo](./virtual-machine-scale-sets-health-extension.md) ou pelas [investigações de integridade do balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md), esse recurso executará automaticamente o reparo da instância excluindo a instância não íntegra e criando uma nova para substituí-la.

> [!NOTE]
> Esse recurso de visualização é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Requisitos para usar os reparos automáticos da instância

**Aceitar a visualização de reparos de instância automática**

Use a API REST ou Azure PowerShell para aceitar a visualização de reparos de instância automática. Essas etapas registrarão sua assinatura para o recurso de visualização. Observe que essa é apenas uma configuração única necessária para usar esse recurso. Se sua assinatura já estiver registrada para a visualização de reparos automáticos da instância, você não precisará se registrar novamente. 

Usando a API REST 

1. Registrar-se para o recurso usando [recursos-registrar](/rest/api/resources/features/register) 

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview/register?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registering"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

2. Aguarde alguns minutos para que o *estado* mude para *registrado*. Você pode usar a API a seguir para confirmar isso.

```
GET on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registered"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

3. Depois que o *estado* for alterado para *registrado*, execute o seguinte.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Usando o PowerShell do Azure

1. Registre-se para o recurso usando o cmdlet [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) seguido por [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Aguarde alguns minutos para que o *registrostate* mude para *registrado*. Você pode usar o cmdlet a seguir para confirmar isso.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 A resposta deve ser a seguinte.

| FeatureName                           | ProviderName            | RegistrationState       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | Registrada              |

3. Depois que o *registrostate* for alterado para *registrado*, execute o cmdlet a seguir.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Habilitar o monitoramento de integridade do aplicativo para o conjunto de dimensionamento**

O conjunto de dimensionamento deve ter o monitoramento de integridade do aplicativo para as instâncias habilitadas. Isso pode ser feito usando a [extensão de integridade do aplicativo](./virtual-machine-scale-sets-health-extension.md) ou as [investigações de integridade do balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md). Somente um deles pode ser habilitado por vez. A extensão de integridade do aplicativo ou as investigações do balanceador de carga executam ping no ponto de extremidade do aplicativo configurado em instâncias de máquina virtual para determinar o status de integridade do aplicativo. Esse status de integridade é usado pelo orquestrador do conjunto de dimensionamento para monitorar a integridade da instância e executar reparos quando necessário.

**Configurar o ponto de extremidade para fornecer o status de integridade**

Antes de habilitar a política de reparos de instância automática, verifique se as instâncias do conjunto de dimensionamento têm o ponto de extremidade do aplicativo configurado para emitir o status de integridade do aplicativo Quando uma instância retorna o status 200 (OK) nesse ponto de extremidade do aplicativo, a instância é marcada como "íntegra". Em todos os outros casos, a instância é marcada como "não íntegra", incluindo os seguintes cenários:

- Quando não há nenhum ponto de extremidade de aplicativo configurado dentro das instâncias de máquina virtual para fornecer o status de integridade do aplicativo
- Quando o ponto de extremidade do aplicativo está configurado incorretamente
- Quando o ponto de extremidade do aplicativo não está acessível

Para instâncias marcadas como "não íntegras", os reparos automáticos são disparados pelo conjunto de dimensionamento. Verifique se o ponto de extremidade do aplicativo está configurado corretamente antes de habilitar a política de reparos automáticos para evitar reparos de instância não intencional, enquanto o ponto de extremidade está sendo configurado.

**Habilitar grupo de posicionamento único**

Essa visualização está disponível no momento apenas para conjuntos de dimensionamento implantados como grupo de posicionamento único. A propriedade *singlePlacementGroup* deve ser definida como *true* para seu conjunto de dimensionamento para usar o recurso de reparos automáticos de instância. Saiba mais sobre [grupos de posicionamento](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**Versão da API**

A política de reparos automáticos tem suporte para a API de computação versão 2018-10-01 ou superior.

**As restrições no recurso ou na assinatura se movem**

Como parte desta visualização, as movimentações de recursos ou assinaturas atualmente não têm suporte para conjuntos de dimensionamento quando a política de reparos automáticos está habilitada.

**Restrição para conjuntos de dimensionamento do Service Fabric**

Atualmente, não há suporte para este recurso de visualização para conjuntos de dimensionamento do Service Fabric.

## <a name="how-do-automatic-instance-repairs-work"></a>Como funcionam os reparos automáticos da instância?

O recurso de reparo automático de instância depende do monitoramento de integridade de instâncias individuais em um conjunto de dimensionamento. As instâncias de VM em um conjunto de dimensionamento podem ser configuradas para emitir o status de integridade do aplicativo usando a [extensão de integridade do aplicativo](./virtual-machine-scale-sets-health-extension.md) ou as investigações de integridade do [balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md). Se uma instância for considerada não íntegra, o conjunto de dimensionamento executará a ação de reparo excluindo a instância não íntegra e criando uma nova para substituí-la. Esse recurso pode ser habilitado no modelo do conjunto de dimensionamento de máquinas virtuais usando o objeto *automaticRepairsPolicy* .

### <a name="batching"></a>Separação em lotes

As operações automáticas de reparo de instância são executadas em lotes. Em um determinado momento, não mais do que 5% das instâncias no conjunto de dimensionamento são reparadas por meio da política de reparos automáticas. Isso ajuda a evitar a exclusão simultânea e a recriação de um grande número de instâncias, se elas não estiverem íntegras ao mesmo tempo.

### <a name="grace-period"></a>Período de carência

Quando uma instância passa por uma operação de alteração de estado devido a uma ação PUT, PATCH ou POST executada no conjunto de dimensionamento (por exemplo, refazer a imagem, reimplantar, atualizar, etc.), qualquer ação de reparo nessa instância é executada somente depois de aguardar o período de carência. O período de carência é a quantidade de tempo para permitir que a instância retorne ao estado íntegro. O período de carência começa após a conclusão da alteração de estado. Isso ajuda a evitar qualquer operação de reparo prematuro ou acidental. O período de carência é respeitado para qualquer instância recém-criada no conjunto de dimensionamento (incluindo aquele criado como resultado da operação de reparo). O período de carência é especificado em minutos no formato ISO 8601 e pode ser definido usando a propriedade *automaticRepairsPolicy. gracePeriod*. O período de carência pode variar entre 30 minutos e 90 minutos e tem um valor padrão de 30 minutos.

O processo de reparos automáticos da instância funciona da seguinte maneira:

1. A [extensão de integridade do aplicativo](./virtual-machine-scale-sets-health-extension.md) ou as investigações de integridade do [balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md) executam ping no ponto de extremidade do aplicativo em cada máquina virtual no conjunto de dimensionamento para obter o status de integridade do aplicativo para cada instância.
2. Se o ponto de extremidade responder com um status 200 (OK), a instância será marcada como "íntegra". Em todos os outros casos (incluindo se o ponto de extremidade está inacessível), a instância é marcada como "não íntegro".
3. Quando uma instância é considerada não íntegra, o conjunto de dimensionamento dispara uma ação de reparo excluindo a instância não íntegra e criando uma nova para substituí-la.
4. Os reparos da instância são executados em lotes. Em um determinado momento, não mais do que 5% do total de instâncias no conjunto de dimensionamento são reparadas. Se um conjunto de dimensionamento tiver menos de 20 instâncias, os reparos serão feitos para uma instância não íntegra de cada vez.
5. O processo acima continua até que todas as instâncias não íntegras no conjunto de dimensionamento sejam reparadas.

## <a name="instance-protection-and-automatic-repairs"></a>Proteção de instância e reparos automáticos

Se uma instância em um conjunto de dimensionamento for protegida aplicando a *[política de proteção proteger contra escala-definir ações](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* , os reparos automáticos não serão executados nessa instância.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Habilitando a política de reparos automáticos ao criar um novo conjunto de dimensionamento

Para habilitar a política de reparos automáticos ao criar um novo conjunto de dimensionamento, certifique-se de que todos os [requisitos](#requirements-for-using-automatic-instance-repairs) para a permissão desse recurso sejam atendidos. O ponto de extremidade do aplicativo deve ser configurado corretamente para instâncias do conjunto de dimensionamento para evitar o disparo de reparos indesejados enquanto o ponto de extremidade está sendo configurado. Para conjuntos de dimensionamento recém-criados, qualquer reparo de instância é executado somente após a espera pela duração do período de carência. Para habilitar o reparo automático de instância em um conjunto de dimensionamento, use o objeto *automaticRepairsPolicy* no modelo do conjunto de dimensionamento de máquinas virtuais.

### <a name="rest-api"></a>API REST

O exemplo a seguir mostra como habilitar o reparo automático de instância em um modelo de conjunto de dimensionamento. Use a API versão 2018-10-01 ou superior.

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

O recurso de reparo automático de instância pode ser habilitado ao criar um novo conjunto de dimensionamento usando o cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) . Este script de exemplo percorre a criação de um conjunto de dimensionamento e os recursos associados usando o arquivo de configuração: [criar um conjunto de dimensionamento de máquinas virtuais completo](./scripts/powershell-sample-create-complete-scale-set.md). Você pode configurar a política de reparos automáticos de instância adicionando os parâmetros *EnableAutomaticRepair* e *AutomaticRepairGracePeriod* ao objeto de configuração para criar o conjunto de dimensionamento. O exemplo a seguir habilita o recurso com um período de carência de 30 minutos.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Habilitando a política de reparos automáticos ao atualizar um conjunto de dimensionamento existente

Antes de habilitar a política de reparos automáticos em um conjunto de dimensionamento existente, verifique se todos os [requisitos](#requirements-for-using-automatic-instance-repairs) para optar por esse recurso foram atendidos. O ponto de extremidade do aplicativo deve ser configurado corretamente para instâncias do conjunto de dimensionamento para evitar o disparo de reparos indesejados enquanto o ponto de extremidade está sendo configurado. Para habilitar o reparo automático de instância em um conjunto de dimensionamento, use o objeto *automaticRepairsPolicy* no modelo do conjunto de dimensionamento de máquinas virtuais.

### <a name="rest-api"></a>API REST

O exemplo a seguir habilita a política com o período de carência de 40 minutos. Use a API versão 2018-10-01 ou superior.

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

Use o cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) para modificar a configuração do recurso de reparo automático de instância em um conjunto de dimensionamento existente. O exemplo a seguir atualiza o período de carência para 40 minutos.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>Solução de problemas

**Falha ao habilitar a política de reparos automáticos**

Se você receber um erro ' BadRequest ' com uma mensagem informando "não foi possível encontrar o membro ' automaticRepairsPolicy ' no objeto do tipo ' Properties '", verifique a versão da API usada para o conjunto de dimensionamento de máquinas virtuais. A versão de API 2018-10-01 ou superior é necessária para este recurso.

**Instância não sendo reparada mesmo quando a política está habilitada**

A instância pode estar no período de carência. Essa é a quantidade de tempo de espera após qualquer alteração de estado na instância antes de executar reparos. Isso é para evitar reparos prematuros ou acidentais. A ação de reparo deve ocorrer depois que o período de carência for concluído para a instância.

**Exibindo o status de integridade do aplicativo para instâncias do conjunto de dimensionamento**

Você pode usar a [API de exibição obter instância](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) para instâncias em um conjunto de dimensionamento de máquinas virtuais para exibir o status de integridade do aplicativo. Com Azure PowerShell, você pode usar o cmdlet [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) com o sinalizador *-InstanceView* . O status de integridade do aplicativo é fornecido sob a propriedade *vmHealth*.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Saiba como configurar a [extensão de integridade do aplicativo](./virtual-machine-scale-sets-health-extension.md) ou as [investigações de integridade do balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md) para seus conjuntos de dimensionamento.
