---
title: Acompanhar o saldo de crédito Azure para um Contrato de Cliente da Microsoft
description: Saiba como verificar o saldo de crédito Azure para um Contrato de Cliente da Microsoft.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ea3fc21891f1e4d4e744449032a4b2cfcdfbb2f0
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177526"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Acompanhar o saldo de crédito Azure no Contrato de Cliente da Microsoft

Verifique o saldo de crédito Azure para o Contrato de Cliente da Microsoft no portal do Azure. Use créditos para pagar pelos encargos que são cobertos pelos créditos.

Você é cobrado quando usa produtos que não são cobertos pelos créditos ou quando seu uso excede o saldo de crédito. Para obter mais informações, confira [Produtos que não são cobertos pelos créditos Azure](#products-that-arent-covered-by-azure-credits).

Este artigo aplica-se a uma conta de cobrança para um Contrato de Cliente da Microsoft. [Verifique se você tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Verificar seu saldo de crédito

1. Entre no [Portal do Azure]( https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança**.

    ![Captura de tela que mostra a pesquisa de Gerenciamento de Custos + Cobrança no portal](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Selecione **Créditos Azure** no lado esquerdo. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança ou um perfil de cobrança e, em seguida, selecionar **créditos Azure**.

4. A página de créditos Azure exibe as seguintes informações:

   ![Captura de tela do saldo de crédito e das transações de um perfil de cobrança](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Termo               | Definição                           |
   |--------------------|--------------------------------------------------------|
   | Saldo estimado  | O valor estimado de créditos que você terá depois de considerar todas as transações faturadas e pendentes |
   | Saldo atual    | Valor de créditos da última fatura. Ele não inclui nenhuma transação pendente |
   | Transações       | Todas as transações de cobrança que afetaram o saldo de crédito Azure |

   Quando o saldo estimado cair para 0, você será cobrado por todo o seu uso, incluindo os produtos cobertos pelos créditos.

6. Selecione **Lista de créditos** para exibir a lista de créditos do perfil de cobrança. A lista de créditos fornece as seguintes informações:

   ![Captura de tela da listas de créditos de um perfil de cobrança](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Termo | Definição |
   |---|---|
   | Saldo estimado | O valor do crédito Azure que você tem depois de subtrair os encargos qualificados de crédito não faturados do saldo atual|
   | Saldo atual | O valor do crédito Azure que você tem antes de considerar os encargos qualificados de crédito não faturados. Ele é calculado com a adição de novos créditos Azure que você recebeu ao saldo de crédito no momento da última fatura|
   | Fonte | A origem da aquisição do crédito |
   | Data de início | A data em que você adquiriu o crédito |
   | Data de validade | A data em que o crédito expira |
   | Saldo | O saldo de sua última fatura |
   | Valor original | O valor original do crédito |
   | Status | O status atual do crédito. O status pode ser ativo, usado, expirado ou a expirar |

## <a name="how-credits-are-used"></a>Como os créditos são usados

Em uma conta de cobrança para um Contrato de Cliente da Microsoft, você usa perfis de cobrança para gerenciar suas faturas e suas formas de pagamento. Uma fatura mensal é gerada para cada perfil de cobrança e você usa as formas de pagamento para pagar a fatura.

Os créditos Azure são uma das formas de pagamento. Você obtém crédito da Microsoft como crédito promocional e crédito no nível de serviço. Esses créditos são atribuídos a um perfil de cobrança. Quando uma fatura é gerada para o perfil de cobrança, os créditos são automaticamente aplicados ao valor total faturado para calcular o valor que você precisa pagar. Você paga o valor restante com outra forma de pagamento, como cheque ou transferência eletrônica.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produtos que não são cobertos pelos créditos Azure

 Os produtos a seguir não são cobertos pelos créditos Azure. Você é cobrado por usar esses produtos, independentemente de seu saldo de crédito:

- Canônico
- Citrix XenApp Essentials
- Citrix XenDesktop
- Usuário registrado
- OpenLogic
- Direitos de Acesso Remoto XenApp Essentials
Usuário Registrado
- Ubuntu Advantage
- Visual Studio Enterprise (mensal)
- Visual Studio Enterprise (anual)
- Visual Studio Professional (mensal)
- Visual Studio Professional (anual)
- Produtos do Azure Marketplace
- Planos de Suporte do Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

- [Entender a conta de cobrança para um Contrato de Cliente da Microsoft](billing-mca-overview.md)
- [Entender os termos em sua fatura do Contrato de Cliente da Microsoft](billing-mca-understand-your-invoice.md)
