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
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274808"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Visualização: Reparos automáticos de instância para conjuntos de escalade máquinas virtuais do Azure

Permitir reparos automáticos de instâncias para conjuntos de escalade máquinas virtuais do Azure ajuda a obter alta disponibilidade para aplicativos, mantendo um conjunto de instâncias saudáveis. Se uma instância no conjunto de escalas for considerada insalubre, conforme relatado pelos testes de [saúde da extensão Application Health](./virtual-machine-scale-sets-health-extension.md) ou do [Balanceador de carga,](../load-balancer/load-balancer-custom-probe-overview.md)esse recurso executa automaticamente o reparo da instância excluindo a instância insalubre e criando uma nova para substituí-la.

> [!NOTE]
> Este recurso de visualização é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Requisitos para o uso de reparos automáticos de instâncias

**Opte pela visualização automática de reparos de instância**

Use a API REST ou o Azure PowerShell para optar pela visualização automática de reparos de instância. Essas etapas registrarão sua assinatura para o recurso de visualização. Observe que esta é apenas uma configuração única necessária para usar este recurso. Se sua assinatura já estiver registrada para visualização automática de reparos de instância, então você não precisa se registrar novamente. 

Usando a API REST 

1. Registre-se para o recurso usando [Recursos - Registre-se](/rest/api/resources/features/register) 

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

2. Aguarde alguns minutos para que o *Estado* mude para *Registrado*. Você pode usar a Seguinte API para confirmar isso.

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

3. Uma vez que o *Estado* tenha mudado para *Registrado,* execute o seguinte.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Usando o PowerShell do Azure

1. Registre-se para o recurso usando cmdlet [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) seguido por [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Aguarde alguns minutos para que o *RegistroEstado* mude para *Registrado*. Você pode usar o seguinte cmdlet para confirmar isso.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 A resposta deve ser a seguinte.

| FeatureName                           | ProviderName            | Registrationstate       |
|---------------------------------------|-------------------------|-------------------------|
| ReparandoVMScaleSetInstancesPreview      | Microsoft.Compute       | Registrada              |

3. Uma vez que o *RegistroSeja* alterar para *Registrado,* execute o cmdlet a seguir.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Habilite o monitoramento de saúde do aplicativo para o conjunto de escalas**

O conjunto de escalas deve ter monitoramento de saúde de aplicação para os casos habilitados. Isso pode ser feito usando a [extensão application health](./virtual-machine-scale-sets-health-extension.md) ou [testes de saúde do balanceador load balancer](../load-balancer/load-balancer-custom-probe-overview.md). Apenas um destes pode ser habilitado de cada vez. A extensão de saúde do aplicativo ou o balanceador de carga testa o ping do ponto final do aplicativo configurado em instâncias de máquinas virtuais para determinar o estado de saúde do aplicativo. Esse estado de saúde é utilizado pelo orquestrador de conjuntos de escalapara monitorar a saúde da ocorrência e realizar reparos quando necessário.

**Configurar ponto final para fornecer status de saúde**

Antes de habilitar a política de reparoautomático de instâncias, certifique-se de que as instâncias de conjunto de escala tenham o ponto final do aplicativo configurado para emitir o status de saúde do aplicativo. Quando uma instância retorna o status 200 (OK) neste ponto final do aplicativo, então a instância é marcada como "Saudável". Em todos os outros casos, a ocorrência está marcada como "Insalubre", incluindo os seguintes cenários:

- Quando não há um ponto final de aplicativo configurado dentro das instâncias da máquina virtual para fornecer o status de saúde do aplicativo
- Quando o ponto final do aplicativo estiver configurado incorretamente
- Quando o ponto final do aplicativo não é acessível

Para casos marcados como "Insalubres", os reparos automáticos são acionados pelo conjunto de escalas. Certifique-se de que o ponto final do aplicativo está configurado corretamente antes de ativar a política de reparos automáticos para evitar reparos de instâncias não intencionais, enquanto o ponto final está sendo configurado.

**Habilitar um único grupo de colocação**

Esta visualização está atualmente disponível apenas para conjuntos de escala implantados como um único grupo de colocação. O *singlePlacementGroup* de propriedade deve ser definido *como verdadeiro* para o seu conjunto de escala sustapara usar o recurso de reparos automáticos de instâncias. Saiba mais sobre [grupos de colocação](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**Versão aPI**

A política de reparos automáticos é suportada para a versão aPI de computação 2018-10-01 ou superior.

**Restrições a recursos ou movimentos de assinatura**

Como parte dessa visualização, os movimentos de recursos ou de assinatura não são suportados atualmente para conjuntos de escala quando a política de reparos automáticos é ativada.

**Restrição para conjuntos de escala de malha de serviço**

No momento, esse recurso de visualização não é suportado para conjuntos de escala de malha de serviço.

## <a name="how-do-automatic-instance-repairs-work"></a>Como funcionam os reparos automáticos de instâncias?

O recurso de reparo automático de instâncias depende do monitoramento de saúde de instâncias individuais em um conjunto de escalas. As instâncias de VM em um conjunto de escalas podem ser configuradas para emitir o status de saúde do aplicativo usando a [extensão Application Health](./virtual-machine-scale-sets-health-extension.md) ou [os testes de saúde do balanceador load .](../load-balancer/load-balancer-custom-probe-overview.md) Se uma instância for considerada insalubre, o conjunto de escalas executa a ação de reparo excluindo a instância insalubre e criando uma nova para substituí-la. Esse recurso pode ser habilitado no modelo de conjunto de escalade máquina virtual usando o objeto *AutomaticRepairsPolicy.*

### <a name="batching"></a>Envio em lote

As operações automáticas de reparo de instâncias são realizadas em lotes. A qualquer momento, não mais de 5% das instâncias no conjunto de escalas são reparadas através da política de reparos automáticos. Isso ajuda a evitar a exclusão simultânea e a recriação de um grande número de casos se considerados insalubres ao mesmo tempo.

### <a name="grace-period"></a>Período de carência

Quando uma instância passa por uma operação de alteração de estado por causa de uma ação PUT, PATCH ou POST realizada no conjunto de escala (por exemplo, reimagem, reimplantação, atualização, etc.), então qualquer ação de reparo nessa instância é executada somente após a espera pelo período de carência. O período de carência é a quantidade de tempo para permitir que a instância retorne ao estado saudável. O período de carência começa após a mudança de estado ter sido concluída. Isso ajuda a evitar qualquer operação de reparo prematura ou acidental. O período de carência é honrado por qualquer instância recém-criada no conjunto de escalas (incluindo a criada como resultado da operação de reparo). O período de carência é especificado em minutos no formato ISO 8601 e pode ser definido usando a propriedade *automáticaRepairsPolicy.gracePeriod*. O período de carência pode variar entre 30 minutos e 90 minutos, e tem um valor padrão de 30 minutos.

O processo automático de reparos de instâncias funciona da seguinte forma:

1. [A extensão de saúde do aplicativo](./virtual-machine-scale-sets-health-extension.md) ou o [balanceador de carga testa](../load-balancer/load-balancer-custom-probe-overview.md) o ponto final do aplicativo dentro de cada máquina virtual na escala definida para obter o status de saúde do aplicativo para cada instância.
2. Se o ponto final responder com um status 200 (OK), então a instância será marcada como "Saudável". Em todos os outros casos (inclusive se o ponto final for inalcançável), a ocorrência está marcada como "Insalubre".
3. Quando uma instância é considerada insalubre, o conjunto de escala susta uma ação de reparo excluindo a instância insalubre e criando uma nova para substituí-la.
4. Os reparos de instâncias são realizados em lotes. A qualquer momento, não mais de 5% do total de instâncias no conjunto de escalas são reparadas. Se um conjunto de escala tiver menos de 20 instâncias, os reparos são feitos para uma instância insalubre de cada vez.
5. O processo acima continua até que todas as instâncias insalubres no conjunto de escala sejam reparadas.

## <a name="instance-protection-and-automatic-repairs"></a>Proteção de instâncias e reparos automáticos

Se uma instância em um conjunto de escala estiver protegida aplicando a *[diretiva de proteção de ações definidas em escala,](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* os reparos automáticos não serão realizados nessa instância.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Habilitar a política de reparos automáticos ao criar um novo conjunto de escalas

Para habilitar a política de reparos automáticos ao criar um novo conjunto de escalas, certifique-se de que todos os [requisitos](#requirements-for-using-automatic-instance-repairs) para optar por esse recurso sejam atendidos. O ponto final do aplicativo deve ser configurado corretamente para instâncias de conjunto de escala para evitar o acionamento de reparos não intencionais enquanto o ponto final estiver sendo configurado. Para conjuntos de escala recém-criados, qualquer reparo de instância é realizado somente após a espera do período de carência. Para habilitar o reparo automático de instâncias em um conjunto de escalas, use o objeto *AutomaticRepairsPolicy* no modelo de conjunto de escalas de máquina virtual.

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

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Habilitar a política de reparos automáticos ao atualizar um conjunto de escalas existente

Antes de habilitar a política de reparos automáticos em um conjunto de escalas existente, certifique-se de que todos os [requisitos](#requirements-for-using-automatic-instance-repairs) para optar por esse recurso sejam atendidos. O ponto final do aplicativo deve ser configurado corretamente para instâncias de conjunto de escala para evitar o acionamento de reparos não intencionais enquanto o ponto final estiver sendo configurado. Para habilitar o reparo automático de instâncias em um conjunto de escalas, use o objeto *AutomaticRepairsPolicy* no modelo de conjunto de escalas de máquina virtual.

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

## <a name="troubleshoot"></a>Solução de problemas

**Falha em habilitar a política de reparos automáticos**

Se você receber um erro 'BadRequest' com uma mensagem informando "Não foi possível encontrar membro 'AutomaticRepairsPolicy' em objeto de 'propriedades' do tipo", verifique a versão da API usada para o conjunto de escala da máquina virtual. A versão aPI 2018-10-01 ou superior é necessária para este recurso.

**Exemplo de não ser reparado mesmo quando a política está ativada**

A instância pode estar em período de carência. Este é o tempo de espera após qualquer alteração de estado na instância antes de realizar os reparos. Isto é para evitar quaisquer reparos prematuros ou acidentais. A ação de reparo deve acontecer assim que o período de carência for concluído para a ocorrência.

**Visualização do status de saúde do aplicativo para instâncias definidas em escala**

Você pode usar a [API Get Instance View](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) para exemplos em uma escala de máquina virtual definida para visualizar o estado de saúde do aplicativo. Com o Azure PowerShell, você pode usar o cmdlet [Get-AzVmsVM](/powershell/module/az.compute/get-azvmssvm) com o sinalizador *-InstanceView.* O estado de saúde do aplicativo é fornecido a propriedade *vmHealth*.

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar os testes de [saúde do application health](./virtual-machine-scale-sets-health-extension.md) ou load balancer para seus [conjuntos](../load-balancer/load-balancer-custom-probe-overview.md) de escala.
