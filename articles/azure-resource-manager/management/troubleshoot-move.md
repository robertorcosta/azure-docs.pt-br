---
title: Solucionar problemas de erros de movimento
description: Use o Azure Resource Manager para mover recursos para um novo grupo de recursos ou uma nova assinatura.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 5a65f7daa0f5e3b1c8c6ddfdbecc0ff7d53e5afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891268"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Solução de problemas de mudança de recursos para um novo grupo de recursos ou assinatura

Este artigo fornece sugestões para ajudar a resolver problemas ao mover recursos.

## <a name="upgrade-a-subscription"></a>Atualizar uma assinatura

Se você realmente quiser atualizar sua assinatura do Azure (por exemplo, mudando de gratuita para pré-paga), você precisará converter sua assinatura.

* Para atualizar uma avaliação gratuita, consulte [Faça o upgrade da sua avaliação gratuita ou da assinatura do Microsoft Imagine Azure para o Pague conforme o uso](../../billing/billing-upgrade-azure-subscription.md).
* Para alterar uma conta de pagamento conforme o uso, consulte [Alterar sua assinatura do Azure Pay-As-You-Go para uma oferta diferente](../../billing/billing-how-to-switch-azure-offer.md).

Se você não puder converter a assinatura, [crie uma solicitação de suporte ao Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selecione **Subscription Management** para o tipo de problema.

## <a name="service-limitations"></a>Limitações de serviço

Alguns serviços exigem considerações adicionais ao mover recursos. Se você estiver movendo os seguintes serviços, certifique-se de verificar as orientações e limitações.

* [Serviços de Aplicativos](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Modelo clássico de implantação](./move-limitations/classic-model-move-limitations.md)
* [Rede](./move-limitations/networking-move-limitations.md)
* [Serviços de Recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Máquinas Virtuais](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Grandes pedidos

Quando possível, quebre grandes movimentações em operações de movimentação separadas. O Resource Manager imediatamente retornará um erro quando houver mais de 800 recursos em uma única operação. No entanto, mover menos de 800 recursos também pode falhar por tempo limite.

## <a name="resource-not-in-succeeded-state"></a>Recurso não sucedido estado

Quando você recebe uma mensagem de erro que indica que um recurso não pode ser movido porque não está em um estado bem sucedido, ele pode realmente ser um recurso dependente que está bloqueando a mudança. Normalmente, o código de erro é **MoveCannotProceedWithResourcesNotInSucceededState**.

Se o grupo de recursos de origem ou destino contiver uma rede virtual, os estados de todos os recursos dependentes da rede virtual serão verificados durante a mudança. A verificação inclui esses recursos direta e indiretamente dependentes da rede virtual. Se algum desses recursos estiver em um estado falido, a mudança está bloqueada. Por exemplo, se uma máquina virtual que usa a rede virtual falhar, a movimentação será bloqueada. A mudança é bloqueada mesmo quando a máquina virtual não é um dos recursos que estão sendo movidos e não está em um dos grupos de recursos para a mudança.

Quando você recebe este erro, você tem duas opções. Mova seus recursos para um grupo de recursos que não tenha uma rede virtual ou [suporte de contato](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Próximas etapas

Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](move-resource-group-and-subscription.md) (Mover recursos para o novo grupo de recursos ou assinatura).
