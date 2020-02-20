---
title: Cancelar sua assinatura do Azure | Microsoft Docs
description: Descreve como cancelar sua assinatura do Azure, como a assinatura de Avaliação Gratuita
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 738eb860857d15f920a7552629c7d83ae6e2c3f3
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199816"
---
# <a name="cancel-your-azure-subscription"></a>Cancelar sua assinatura do Azure

Será possível cancelar sua assinatura do Azure no portal do Azure se você não precisar mais da assinatura.

Antes de cancelar sua assinatura:
* Faça backup dos dados. Por exemplo, se você estiver colocando dados no armazenamento do Azure ou SQL, baixe uma cópia. Se você tiver uma máquina virtual, salve uma imagem dela localmente.
* Finalize seus serviços. Vá para a [página de recursos no portal de gerenciamento](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), e **Pare** quaisquer máquinas virtuais, aplicativos ou outros serviços em execução.
* Considere migrar seus dados. Consulte [Mover recursos para um novo grupo de recursos ou uma nova assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Exclua todos os recursos e todos os grupos de recursos. A exclusão deles é necessária antes que você possa cancelar uma assinatura. Cada grupo de recursos deve ser excluído individualmente. Durante a exclusão do grupo de recursos, é necessário confirmar a exclusão digitando o nome do grupo de recursos.
* Se você tiver funções personalizadas que fazem referência a essa assinatura no `AssignableScopes`, deverá atualizá-las para remover a assinatura. Se tentar atualizar uma função personalizada após cancelar uma assinatura, você poderá receber um erro. Para obter mais informações, confira [Solucionar problemas com funções personalizadas](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) e [Funções personalizadas dos recursos do Azure](../../role-based-access-control/custom-roles.md).

Se você cancelar um plano de Suporte do Azure pago, será cobrado pelo restante do período de assinatura. Para obter mais informações, consulte [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Cancelar assinatura no portal do Azure

1. Selecione sua assinatura na [página Assinaturas no Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Selecione a assinatura que você quer cancelar.
3. Selecione **Visão Geral** e, em seguida, selecione **Cancelar assinatura**.
    ![Captura de tela que mostra o botão Cancelar](./media/cancel-azure-subscription/cancel_ibiza.png)
3. Siga os prompts e conclua o cancelamento.


## <a name="who-can-cancel-a-subscription"></a>Quem pode cancelar uma assinatura?

A tabela abaixo descreve a permissão necessária para cancelar uma assinatura.

|Tipo de assinatura     |Quem pode cancelar  |
|---------|---------|
|Assinaturas criadas quando você se inscreve no Azure por meio do site do Azure. Por exemplo, quando você se inscreve em uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), uma [conta com taxas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Administrador, proprietários e colaboradores da conta para a assinatura  |
|[Contrato Enterprise da Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/) e [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Proprietário, proprietários e colaboradores da conta para a assinatura       |
|[Plano do Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) e [Plano do Azure para DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Proprietários e colaboradores da assinatura      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>O que acontecerá depois que eu cancelar minha assinatura?

Após o cancelamento, a cobrança será interrompida imediatamente. No entanto, pode levar até 10 minutos para que o cancelamento seja mostrado no portal. Se você cancelar no meio de um período de cobrança, enviaremos a fatura final na data da fatura normal após o término do período.

Após o cancelamento, seus serviços serão desabilitados. Isso significa que as máquinas virtuais são desalocadas, os endereços IP temporários são liberados e o armazenamento é somente leitura.

Aguardaremos 90 dias antes de excluir permanentemente seus dados caso você precise acessá-los ou mude de ideia. Você não é cobrado pela retenção dos dados. Para saber mais, consulte a [Central de Confiabilidade da Microsoft – Como gerenciamos seus dados](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Reativar a assinatura

Se você cancelar acidentalmente sua assinatura com taxas pagas conforme o uso, poderá [reativá-la no Centro de Contas](subscription-disabled.md).

Caso sua assinatura não tenha taxas pagas conforme o uso, entre em contato com o suporte em até 90 dias após o cancelamento para reativar a assinatura.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
