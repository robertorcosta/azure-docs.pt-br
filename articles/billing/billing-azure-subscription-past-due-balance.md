---
title: Email de saldo vencido do Azure
description: Descreve como fazer um pagamento se a sua assinatura do Azure tiver um saldo devido anterior
author: genlin
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: banders
ms.openlocfilehash: c042f5cee3d0a1e874729911a2162e48c1630a72
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226419"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Resolver o saldo vencido de sua assinatura do Azure

Este artigo se aplica a clientes que têm uma conta do Programa Microsoft Online Services.

Caso seu pagamento não seja recebido ou caso não seja possível processar seu pagamento, você poderá receber um email ou ver um alerta no portal do Azure ou no Centro de Contas.
Se você for o [Administrador da Conta](billing-subscription-transfer.md#whoisaa), poderá liquidar os encargos pendentes no [portal do Azure](https://portal.azure.com). Se você estiver em um modo de pagamento de fatura, envie o pagamento para o local indicado na parte inferior da fatura.

> [!IMPORTANT]
> * Se você tiver várias assinaturas que usam o mesmo cartão de crédito e todas estiverem vencidas, deverá pagar todo o saldo pendente de uma vez.
> * O meio de pagamento que você usar para liquidar os encargos pendentes se tornará a nova forma de pagamento ativa para todas as assinaturas que estavam usando a forma de pagamento com falha.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Resolver saldo vencido no portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) como Administrador de Contas.
1. Pesquise **Gerenciamento de Custos + Cobrança**.
1. Na página Visão Geral, você verá uma lista das suas assinaturas. Se o status da assinatura for vencido, clique no link **Liquidar saldo**.
    ![Captura de tela que mostra o link liquidar saldo](./media/billing-azure-subscription-past-due-balance/settle-balance-entry-point.png)
1. O saldo pendente total reflete os encargos pendentes em todos os serviços da Microsoft que usam a forma de pagamento com falha.
1. Selecione uma forma de pagamento para pagar o saldo. Essa forma de pagamento se tornará a forma ativa para todas as assinaturas que usam a forma de pagamento com falha no momento.
    ![Captura de tela que mostra o link selecionar forma de pagamento](./media/billing-azure-subscription-past-due-balance/settle-balance-screen.png)
1. Se a forma de pagamento selecionada também tiver encargos pendentes para serviços da Microsoft, isso se refletirá no saldo total pendente. Também é necessário pagar esses encargos pendentes.
1. Clique em **Pagar**.

## <a name="troubleshoot-declined-credit-card"></a>Solucionar problemas de cartão de crédito recusado

Se a cobrança em seu cartão de crédito for recusada pela instituição financeira, entre em contato com a instituição financeira para resolver o problema. Verifique com o banco para se certificar de que:
- Transações internacionais estão habilitadas no cartão.
- O cartão tem limite de crédito ou fundos suficientes para liquidar o saldo.
- Pagamentos recorrentes estão habilitados no cartão.

## <a name="not-getting-billing-email-notifications"></a>Não está recebendo notificações de email de cobrança?

Se você for o Administrador da Conta, [verifique qual endereço de email é usado para notificações](billing-how-to-change-azure-account-profile.md). Recomendamos que você use um endereço de email que é verificado regularmente. Se o email estiver correto, verifique a pasta de spam.

## <a name="if-i-forget-to-pay-what-happens"></a>Se eu me esquecer de pagar, o que acontecerá?

O serviço será cancelado e seus recursos não estarão mais disponíveis. Todos os seus dados do Azure serão excluídos 90 dias após o encerramento do serviço. Para saber mais, consulte a [Central de Confiabilidade da Microsoft – Como gerenciamos seus dados](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Caso esteja ciente de que o pagamento foi processado, mas sua assinatura ainda estiver desabilitada, entre em contato com o [Suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
