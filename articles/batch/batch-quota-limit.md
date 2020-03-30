---
title: Cotas do serviço e limites - Lote do Azure | Microsoft Docs
description: Saiba mais sobre as restrições, limites e cotas padrão do Lote do Azure e como aumentar a cota da solicitação
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c8a78acfa1f3e7332cee337ae1a82ee365fe356a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248223"
---
# <a name="batch-service-quotas-and-limits"></a>Cotas e limites de serviço do Lote

Como com outros serviços do Azure, há limites em determinados recursos associados ao serviço de Lote. Muitos desses limites são cotas padrão aplicadas pelo Azure no nível da assinatura ou da conta. Este artigo discute esses padrões e como você pode solicitar aumentos de cotas.

Lembre-se dessas cotas quando estiver projetando e dimensionando suas cargas de trabalho do Lote. Por exemplo, se seu pool não alcançar o número de destino de nós de computação especificado, talvez você tenha atingido o limite de cota de núcleos para sua conta do Lote.

Você pode executar várias cargas de trabalho do Lote em uma única conta do Lote ou distribuir suas cargas de trabalho entre contas do Lote que estão na mesma assinatura mas em diferentes regiões do Azure.

Se você planeja executar cargas de trabalho de produção em Lote, talvez seja necessário aumentar uma ou mais cotas para acima do padrão. Se desejar aumentar a cota, você poderá abrir uma [solicitação de atendimento ao cliente](#increase-a-quota) online gratuitamente.

## <a name="resource-quotas"></a>Cotas de recursos

Uma cota é um limite de crédito, não uma garantia de capacidade. Se você precisar de capacidade em larga escala, entre em contato com o suporte do Azure.

Observe também que as cotas não são valores garantidos. As cotas podem variar de acordo com alterações do serviço Batch ou uma solicitação do usuário para alterar um valor de cota.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Cotas de núcleo no modo de assinatura de usuário

Se você tiver criado uma conta de Lote com modo de alocação de pool definido como **assinatura de usuário**, as cotas serão aplicadas de forma diferente. Nesse modo, as VMs do Lote e outros recursos são criados diretamente em sua assinatura, quando um pool é criado. As cotas de núcleos de Lote do Azure não se aplicam a uma conta criada nesse modo. Em vez disso, as cotas em sua assinatura para regionais núcleos de computação e outros recursos são aplicados. Saiba mais sobre a [assinatura do Azure e limites de serviços, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Limites de tamanho do pool

Os limites de tamanho da piscina são definidos pelo serviço Batch. Ao contrário [das cotas de recursos,](#resource-quotas)esses valores não podem ser alterados. Apenas piscinas com comunicação entre nó e imagens personalizadas têm restrições diferentes da cota padrão.

| **Recurso** | **Limite Máximo** |
| --- | --- |
| **Nós de computação em [pool de computação entre nós habilitado](batch-mpi.md)**  ||
| Modo de alocação de pool de serviço de Lote | 100 |
| Modo de alocação de pool de assinatura do Lote | 80 |
| **Calcular nós no [pool criado susta is](batch-custom-images.md)**<sup>1</sup> ||
| Nós dedicados | 2000 |
| Nós de baixa prioridade | 1000 |

<sup>1</sup> Para pools que não têm comunicação entre nós ativada.

## <a name="other-limits"></a>Outros limites

Limites adicionais definidos pelo serviço Batch. Ao contrário [das cotas de recursos,](#resource-quotas)esses valores não podem ser alterados.

| **Recurso** | **Limite Máximo** |
| --- | --- |
| [Tarefas simultâneas](batch-parallel-node-tasks.md) por nó de computação | 4 vezes o número de núcleos de nó |
| [Aplicativos](batch-application-packages.md) por conta do Lote | 20 |
| Pacotes de aplicativos por aplicativo | 40 |
| Pacotes de aplicativos por pool | 10 |
| Tempo de vida máximo da tarefa | 180 dias<sup>1</sup> |
| [Montagens](virtual-file-mount.md) por nó computacional | 10 |

<sup>1</sup> O tempo de vida máximo de uma tarefa, desde quando é adicionada ao trabalho até ser concluída, é de 180 dias. As tarefas concluídas persistem por sete dias; dados para tarefas não concluídas dentro da vida máxima não são acessíveis.

## <a name="view-batch-quotas"></a>Exibir cotas do Lote

Exibir suas cotas de conta do Lote no [portal do Azure][portal].

1. Selecione **Contas do Lote** no portal e selecione a conta do Lote na qual você está interessado.
1. Selecione **Cotas** no menu da conta do Lote.
1. Exibe as cotas atualmente aplicadas à conta do Lote

    ![Cotas para conta do Lote][account_quotas]

## <a name="increase-a-quota"></a>Aumentar uma cota

Siga estas etapas para solicitar uma cota aumentam para sua conta de lote ou sua assinatura usando o [portal do Azure][portal]. O tipo de aumento de cota depende do modo de alocação de pool de sua conta do lote. Para solicitar um aumento de cota, você deve incluir a série VM para a a que você gostaria de aumentar a cota. Quando o aumento da cota é aplicado, ele é aplicado a todas as séries de VMs.

### <a name="increase-cores-quota-in-batch"></a>Aumentar a cota de núcleos em Lote 

1. Selecione o bloco **Ajuda + suporte** no painel do portal ou o ponto de interrogação (**?**) no canto superior direito do portal.
1. Selecione **Novo pedido de** > suporte**Básico**.
1. Em **Noções básicas**:
   
    a. **Issue Type** > Serviço de tipo**de emissão e limites de assinatura (cotas)**
   
    b. Selecione sua assinatura.
   
    c. **Quota type** > **Lote** tipo cota
      
    Selecione **Avançar**.
    
1. Em **Detalhes**:
      
    a. Em **Fornecer detalhes,** especifique o local, o tipo de cota e a conta do lote.
    
    ![Aumento da cota de lotes][quota_increase]

    Os tipos de cotas incluem:

    * **Por conta de lote**  
        Valores específicos para uma única conta em lote, incluindo núcleos dedicados e de baixa prioridade, e número de empregos e pools.
        
    * **Por região**  
        Valores que se aplicam a todas as contas batch em uma região e inclui o número de contas em lote por região por assinatura.

    Cota de baixa prioridade é um único valor em todas as séries de VM. Se você precisar de SKUs restritos, você deve selecionar **núcleos de baixa prioridade** e incluir as famílias de VM para solicitar.

    b. Selecione uma **Gravidade** de acordo com o [impacto nos negócios][support_sev].

    Selecione **Avançar**.

1. Em **Informações de contato**:
   
    a. Selecione um **método de contato preferencial**.
   
    b. Verifique e insira os detalhes de contato necessários.
   
    Selecione **Criar** para enviar a solicitação de suporte.

Depois que a solicitação de suporte foi enviada, o suporte do Azure entrará em contato com você. As solicitações de cotas podem ser concluídas em poucos minutos ou até dois dias úteis.

## <a name="related-quotas-for-vm-pools"></a>Cotas relacionadas para pools VM

Os pools de lota na Configuração de Máquina virtual implantada em uma rede virtual do Azure automaticamente aloca os recursos de rede adicionais do Azure. Os recursos a seguir são necessárias para cada nós de pool de 50 em uma rede virtual:

* Um [grupo de segurança de rede](../virtual-network/security-overview.md#network-security-groups)
* Um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Um [balanceador de carga](../load-balancer/load-balancer-overview.md)

Esses recursos são alocados na assinatura que contém a rede virtual fornecida ao criar o pool de Lote. Esses recursos são limitados pelas [cotas de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md) da assinatura. Se você planejar implantações de grande pool em uma rede virtual, verifique as cotas da assinatura para esses recursos. Se necessário, solicite um aumento no portal do Azure, selecionando **Ajuda + suporte**.


## <a name="related-topics"></a>Tópicos relacionados
* [Crie uma conta do Azure Batch usando o portal Azure](batch-account-create-portal.md)
* [Visão geral dos recursos do Lote do Azure](batch-api-basics.md)
* [Assinatura do Azure e limite de serviços, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
