---
title: Criar um conjunto de escalas que usa VMs do Azure Spot (Preview)
description: Aprenda a criar conjuntos de escala de máquinas virtuais do Azure que usam VMs spot para economizar custos.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 37e914fe6bafe9587be525faf3e01c897cdd8230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162677"
---
# <a name="preview-azure-spot-vms-for-virtual-machine-scale-sets"></a>Visualização: VMs do Azure Spot para conjuntos de escala de máquinas virtuais 

O uso do Azure Spot em conjuntos de escala permite que você aproveite nossa capacidade não utilizada com uma economia significativa de custos. A qualquer momento em que o Azure precisar da capacidade de volta, a infra-estrutura do Azure despejará instâncias do Spot. Portanto, as instâncias spot são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lote, ambientes de dev/teste, grandes cargas de trabalho computacionais e muito mais.

A quantidade de capacidade disponível pode variar de acordo com o tamanho, região, hora do dia e muito mais. Ao implantar instâncias spot em conjuntos de escala, o Azure alocará a instância somente se houver capacidade disponível, mas não há SLA para esses casos. Um conjunto de escala spot é implantado em um único domínio de falha e não oferece garantias de alta disponibilidade.

> [!IMPORTANT]
> As instâncias spot estão atualmente em visualização pública.
> Esta versão de visualização não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="pricing"></a>Preços

Os preços para instâncias spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


Com preços variáveis, você tem a opção de definir um preço máximo, em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, `0.98765`o valor seria um preço máximo de US$ 0,98765 por hora. Se você definir o `-1`preço máximo para ser , a instância não será despejada com base no preço. O preço, por exemplo, será o preço atual para spot ou o preço para uma instância padrão, que nunca é menor, desde que haja capacidade e cota disponíveis.

## <a name="eviction-policy"></a>Política de despejo

Ao criar conjuntos de escala spot, você pode definir a política de despejo para *Delocar (padrão)* ou *Excluir*. 

A política *Deallocate* move suas instâncias despejadas para o estado desalocado parado, permitindo que você reimplante instâncias despejadas. No entanto, não há nenhuma garantia de que a alocação terá êxito. As VMs deslocadas afetarão sua cota de instância do conjunto de dimensionamento e você será cobrado pelos discos subjacentes. 

Se você quiser que suas instâncias na escala Spot sejam excluídas quando elas forem despejadas, você pode definir a política de despejo para *excluir*. Com a política de remoção definida para excluir, você pode criar novas VMs, aumentando a propriedade de contagem de instância do conjunto de dimensionamento. As VMs removidas são excluídas junto com seus discos subjacentes e, portanto, você não será cobrado pelo armazenamento. Você também pode usar o recurso de dimensionamento automático dos conjuntos de dimensionamento para tentar e compensar automaticamente as VMs removidas, mas não há nenhuma garantia de que a alocação terá êxito. Recomenda-se que você use apenas o recurso de escala automática em conjuntos de escala spot quando você definir a política de despejo para excluir para evitar o custo de seus discos e atingir os limites de cotas. 

Os usuários podem optar por receber notificações in-VM através [do Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md). Isso irá notificá-lo se suas VMs estão sendo despejadas e você terá 30 segundos para terminar qualquer trabalho e executar tarefas de desligamento antes do despejo. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Implantação de VMs spot em conjuntos de escala

Para implantar VMs spot em conjuntos de escala, você pode definir a nova bandeira *Priority* como *Spot*. Todas as VMs do seu conjunto de escalas serão definidas como Spot. Para criar um conjunto de escalas com VMs spot, use um dos seguintes métodos:
- [Portal Azure](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Modelos do Azure Resource Manager](#resource-manager-templates)

## <a name="portal"></a>Portal

O processo para criar um conjunto de escalas que usa VMs Spot é o mesmo que detalhado no [artigo de início](quick-create-portal.md). Quando você está implantando um conjunto de escalas, você pode ![optar por definir a bandeira Spot e a política de despejo: Criar um conjunto de escalas com VMs spot](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>CLI do Azure

O processo para criar um conjunto de escalas com VMs Spot é o mesmo que detalhado no [artigo de início](quick-create-cli.md). Basta adicionar o '-Ponto de `--max-price`Prioridade', e adicionar . Neste exemplo, usamos `-1` `--max-price` para que a instância não seja despejada com base no preço.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

O processo para criar um conjunto de escalas com VMs Spot é o mesmo que detalhado no [artigo de início](quick-create-powershell.md).
Basta adicionar '-Ponto de Prioridade', e fornecer a `-max-price` ao [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos

O processo para criar um conjunto de escalas que usa VMs Spot é o mesmo que detalhado no artigo de início para [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). 

Para implantações de`"apiVersion": "2019-03-01"` modelo spot, use ou posteriormente. Adicione `priority`as `evictionPolicy` `billingProfile` propriedades e `"virtualMachineProfile":` propriedades à seção em seu modelo: 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

Para excluir a instância depois de despejada, altere o `evictionPolicy` parâmetro para `Delete`.

## <a name="faq"></a>Perguntas frequentes

**Q:** Uma vez criado, uma instância spot é a mesma que a instância padrão?

**A:** Sim, exceto que não há SLA para VMs spot e eles podem ser despejados a qualquer momento.


**Q:** O que fazer quando for despejado, mas ainda precisa de capacidade?

**A:** Recomendamos que você use VMs padrão em vez de VMs Spot se você precisar de capacidade imediatamente.


**Q:** Como a cota é gerenciada para o Spot?

**A:** As instâncias spot e as instâncias padrão terão pools de cotas separados. A cota spot será compartilhada entre VMs e instâncias definidas em escala. Para saber mais, confira [Assinatura e limites de serviço, cotas e restrições do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**Q:** Posso solicitar uma cota adicional para spot?

**A:** Sim, você poderá enviar a solicitação para aumentar sua cota de VMs Spot através do [processo padrão de solicitação de cotas.](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)


**Q:** Posso converter conjuntos de escala existentes em conjuntos de escala spot?

**A:** Não, a `Spot` configuração da bandeira só é suportada no momento da criação.


**Q:** Se eu `low` estava usando para conjuntos de escala `Spot` de baixa prioridade, eu preciso começar a usar em vez disso?

**A:** Por enquanto, ambos `low` e `Spot` funcionarão, mas `Spot`você deve começar a transição para usar .


**Q:** Posso criar um conjunto de escalas com VMs regulares e VMs spot?

**A:** Não, um conjunto de escalas não pode suportar mais de um tipo de prioridade.


**Q:**  Posso usar escala automática com conjuntos de escala Spot?

**A:** Sim, você pode definir regras de autodimensionamento no conjunto de escala seleíope. Se suas VMs forem despejadas, a escala automática pode tentar criar novas VMs spot. Lembre-se de que essa capacidade não é garantida. 


**Q:**  A escala automática funciona com ambas as políticas de despejo (desalocar e excluir)?

**A:** Recomenda-se que você defina sua política de despejo para excluir ao usar escala automática. Isso ocorre porque instâncias desalocadas são contadas em relação a sua capacidade de contagem no conjunto de dimensionamento. Ao usar o dimensionamento automático, você provavelmente atingirá sua contagem de instâncias de destino rapidamente devido a instâncias desalocadas, removidas. 


**Q:** Quais canais suportam VMs spot?

**A:** Veja a tabela abaixo para disponibilidade spot VM.

<a name="channel"></a>

| Canais Azure               | Disponibilidade de VMs do Azure Spot       |
|------------------------------|-----------------------------------|
| Contrato Enterprise         | Sim                               |
| Pago Conforme o Uso                | Sim                               |
| Provedor de Serviços de Nuvem (CSP) | [Entre em contato com seu parceiro](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Benefícios                     | Não disponível                     |
| Patrocinado                    | Não disponível                     |
| Avaliação gratuita                   | Não disponível                     |


**Q:** Onde posso postar perguntas?

**A:** Você pode postar e `azure-spot` marcar sua pergunta com [em Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Próximas etapas
Agora que você criou um conjunto de escalas com VMs Spot, tente implantar nosso [modelo de escala automática usando o Spot](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Confira a [página de preços de conjunto de dimensionamento de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) para obter detalhes sobre preços.
