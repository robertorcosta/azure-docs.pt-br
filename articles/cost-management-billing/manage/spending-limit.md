---
title: Limite de gastos do Azure | Microsoft Docs
description: Este artigo descreve como funciona o limite de gastos do Azure e como removê-lo.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: banders
ms.openlocfilehash: 5dbf885848d9866a184caee1da6b9000a26f83a9
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76314108"
---
# <a name="azure-spending-limit"></a>Limite de gastos do Azure

O limite de gastos do Azure impede gastos sobre a sua quantidade de créditos. Todos os novos clientes que se inscrevem em uma conta gratuita do Azure ou em tipos de assinaturas que incluem créditos durante vários meses têm o limite de gastos ativado por padrão. O limite de gastos é igual ao valor de crédito e não pode ser alterado. Por exemplo, se você se inscreveu em uma conta gratuita do Azure, seu limite de gastos é de US$ 200 e você não pode alterá-lo para US$ 500. No entanto, você pode remover o limite de gastos. Portanto, você não tem nenhum limite ou tem um limite igual ao valor de crédito. Isso impede que você tenha mais tipos de gastos. O limite de gastos não está disponível para assinaturas com planos de compromisso ou com preços pagos conforme o uso. Confira a [lista completa de tipos de assinatura do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Alcance de um limite de gastos

Quando o seu uso resultar em encargos que consomem o limite de gastos, os serviços implantados são desabilitados durante o restante do período de cobrança.

Por exemplo, quando você gasta todo o crédito incluído em sua conta gratuita do Azure, os recursos do Azure implantados são removidos da produção e suas máquinas virtuais do Azure são interrompidas e desalocadas. Os dados de suas contas de armazenamento estão disponíveis como somente leitura.

Se o tipo de assinatura incluir créditos durante vários meses, a assinatura será habilitada novamente de maneira automática no início do próximo período de cobrança. Depois, você pode reimplantar os recursos do Azure e ter acesso completo às contas de armazenamento e aos bancos de dados.

O Azure envia notificações por email quando você atinge o limite de gastos. Entre no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) para ver notificações sobre as assinaturas que atingiram o limite de gastos.

Se você se inscreveu em uma conta gratuita do Azure e atinge o limite de gastos, faça a atualização para um preço [pago conforme o uso](upgrade-azure-subscription.md) para remover o limite de gastos e habilitar novamente sua assinatura de maneira automática.

## <a name="remove-the-spending-limit-in-azure-portal"></a>Remover o limite de gastos no portal do Azure

<a id="remove"></a>

Você poderá remover o limite de gastos a qualquer momento, contanto que haja uma forma de pagamento válida associada à sua assinatura do Azure. Para os tipos de assinatura que têm créditos durante vários meses, como o Visual Studio Enterprise e o Visual Studio Professional, você também pode habilitar o limite de gastos no início do próximo período de cobrança.

Para remover seu limite de gastos, execute estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Navegue até **Assinaturas**.
1. Selecione sua assinatura. Por exemplo, *Visual Studio Enterprise*.
1. Na parte superior da página, selecione **Gerenciar**. Você será redirecionado para https://account.azure.com/. No lado direito da página, selecione **Remover limite de gastos**.  
  ![Exemplo mostrando a opção Remover limite de gastos](./media/spending-limit/account-azure-com-spending-limit.png)
1. Você será redirecionado de volta ao portal do Azure. Selecione uma opção apropriada para você e, em seguida, selecione uma forma de pagamento. Selecione **Concluir**.  
  ![Selecionar uma opção para remover o limite de gastos](./media/spending-limit/remove-spending-limit.png)

| Opção | Efeito |
| --- | --- |
| Remover o limite de gastos indefinidamente | Remove o limite de gastos sem ativá-lo automaticamente no início do próximo período de cobrança. |
| Remover limite de gastos para o período de cobrança atual | Remove o limite de gastos para que ele seja ativado automaticamente no início do próximo período de cobrança. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Por que remover o limite de gastos?

O limite de gastos pode evitar que você implante ou use determinados serviços da Microsoft e de terceiros. Veja algumas situações em que você poderia remover o limite de gastos da sua assinatura.

-  Você pretende implantar imagens internas como o Oracle e serviços como o Azure DevOps Services. Essa situação faz com que você atinja seu limite de gastos quase imediatamente e que a sua assinatura seja desabilitada.
- Há alguns serviços que não podem ser interrompidos. Quando você atinge seu limite de gastos, os recursos do Azure implantados são removidos da produção e suas máquinas virtuais do Azure são interrompidas e desalocadas. Caso você tenha serviços que não deseja que sejam interrompidos, precisará remover seu limite de gastos.
- Você tem serviços e recursos com configurações como endereços IP virtuais e que não deseja perder. Essas configurações são perdidas quando você atinge seu limite de gastos e os serviços e os recursos são desalocados.

## <a name="turn-on-the-spending-limit-after-removing"></a>Ativar o limite de gastos após removê-lo

Esse recurso só está disponível quando o limite de gastos é removido por tempo indefinido para tipos de assinatura que incluem créditos durante vários meses. Use esse recurso para ativar seu limite de gastos automaticamente no início do próximo período de cobrança.

1. Entre no [Centro de Contas](https://account.windowsazure.com/Subscriptions).
1. Selecione a faixa amarela para alterar a opção de limite de gastos.
1. Escolha **Ativar o limite de gastos no próximo período de cobrança \<start date of billing period\>**

## <a name="custom-spending-limit"></a>Limite de gastos personalizado

Limites de gastos personalizados não estão disponíveis.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Um limite de gastos não impede todos os encargos

[Alguns serviços externos publicados no Azure Marketplace](../understand/understand-azure-marketplace-charges.md) não podem ser usados com seus créditos de assinatura, podendo gerar cobranças independentes, mesmo quando o limite de gastos estiver definido. Exemplos incluem licenças do Visual Studio, o Azure Active Directory Premium, planos de suporte e a maior parte dos serviços de terceiros. Quando você provisiona um novo serviço externo, é mostrado um aviso informando que os serviços são cobrados separadamente:

![Aviso de compra no Marketplace](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- Atualize para um plano com o preço [pago conforme o uso](upgrade-azure-subscription.md).
