---
title: Concluir as tarefas do Contrato Enterprise no Contrato de Cliente da Microsoft – Azure
description: Saiba como concluir as tarefas do Contrato Enterprise em sua nova conta de cobrança.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ca0f83628edeef9e55ebc81798d0229791b80a7a
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375496"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Concluir as tarefas do Contrato Enterprise em sua conta de cobrança para um Contrato de Cliente da Microsoft

Caso sua organização tenha assinado um Contrato de Cliente da Microsoft para renovar o registro do Contrato Enterprise, uma conta de cobrança será criada para o contrato. A cobrança em sua nova conta é organizada de maneira diferente do Contrato Enterprise. Este artigo descreve como você pode usar a nova conta de cobrança para executar tarefas que você realizava no Contrato Enterprise.

## <a name="billing-organization-in-the-new-account"></a>Organização da cobrança na nova conta

O diagrama a seguir descreve como a cobrança é organizada em sua nova conta de cobrança.

![Imagem da hierarquia do EA-MCA pós-transição](./media/mca-setup-account/mca-post-transition-hierarchy.png)

| Contrato Enterprise   | Contrato de Cliente da Microsoft    |
|------------------------|--------------------------------------------------------|
| Registro            | Você usará um perfil de cobrança para gerenciar a cobrança de sua organização, de maneira semelhante ao registro do Contrato Enterprise. Os administradores corporativos se tornam proprietários do perfil de cobrança. Para saber mais sobre os perfis de cobrança, confira [Entender os perfis de cobrança](billing-mca-overview.md#billing-profiles).
| department            | Você usará uma seção da fatura para organizar os custos, de maneira semelhante aos departamentos no registro do Contrato Enterprise. O departamento se torna as seções da fatura e os administradores do departamento se tornam proprietários das respectivas seções da fatura. Para saber mais sobre as seções da fatura, confira [Entender as seções da fatura](billing-mca-overview.md#invoice-sections). |
| Conta               | Não há suporte na nova conta de cobrança para as contas que foram criadas no Contrato Enterprise. As assinaturas da conta pertencem à respectiva seção da fatura de seus departamentos. Os proprietários da conta podem criar e gerenciar assinaturas para as seções da fatura. |

## <a name="changes-for-enterprise-administrators"></a>Alterações para administradores corporativos

As alterações a seguir se aplicam aos administradores corporativos em um Contrato Enterprise que foi renovado para um Contrato de Cliente da Microsoft.

- Um perfil de cobrança é criado para seu registro. Você usará o perfil de cobrança para gerenciar a cobrança de sua organização, de maneira semelhante ao registro do Contrato Enterprise. Para saber mais sobre os perfis de cobrança, confira [Entender os perfis de cobrança](billing-mca-overview.md#billing-profiles).
- Uma seção da fatura é criada para cada departamento no registro do Contrato Enterprise. Você usará as seções da fatura para gerenciar os departamentos. Você pode criar seções da fatura para configurar departamentos adicionais. Para saber mais sobre as seções da fatura, confira [Entender as seções da fatura](billing-mca-overview.md#invoice-sections).
- Você usará a função de criador de assinatura do Azure nas seções da fatura para conceder a outros usuários a permissão para criar uma assinatura do Azure, como as contas que foram criadas no registro do Contrato Enterprise.
- Você usará o [portal do Azure](https://portal.azure.com) para gerenciar a cobrança de sua organização, em vez do portal do Azure EA.

Você recebe as seguintes funções na nova conta de cobrança:

**Proprietário do perfil de cobrança** – você recebe a função de proprietário do perfil de cobrança no perfil de cobrança que foi criado quando o contrato foi assinado. A função permite que você gerencie a cobrança de sua organização. Você pode exibir encargos e faturas, organizar os custos na fatura, gerenciar formas de pagamento e controlar o acesso à cobrança de sua organização.

**Proprietário da seção da fatura** – você recebe a função de proprietário da seção da fatura em todas as seções da fatura criadas para os departamentos em seu registro do Contrato Enterprise. A função permite que você controle quem pode criar assinaturas do Azure e comprar outros produtos.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>Exibir saldo de créditos e encargos de sua organização

Você usará seu perfil de cobrança para acompanhar os encargos e o saldo de créditos Azure de sua organização, de maneira semelhante ao registro do Contrato Enterprise.

Para saber como exibir o saldo de crédito de um perfil de cobrança, confira [Acompanhar o saldo de crédito Azure de seu perfil de cobrança](billing-mca-check-azure-credits-balance.md).

Para saber como exibir os encargos de um perfil de cobrança, confira [Entender os encargos em sua fatura do Contrato de Cliente da Microsoft](billing-mca-understand-your-bill.md).

### <a name="view-charges-for-a-department"></a>Exibir os encargos de um departamento

Uma seção da fatura é criada para cada departamento que você tinha no Contrato Enterprise. Exiba os encargos de uma seção da fatura no portal do Azure. Para obter mais informações, confira [Exibir transações por seções da fatura](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

### <a name="view-charges-for-an-account"></a>Exibir os encargos de uma conta

Não há suporte na nova conta de cobrança para as contas que foram criadas no registro do Contrato Enterprise. As assinaturas da conta pertencem à respectiva seção da fatura de seus departamentos. Os proprietários da conta podem criar e gerenciar assinaturas para as seções da fatura.

Para exibir o custo agregado de assinaturas que pertenciam a uma conta, é necessário definir um centro de custo para cada assinatura. Em seguida, use o arquivo CSV de uso e encargos do Azure para filtrar as assinaturas pelo centro de custo.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>Baixar o CSV de uso e encargos, a planilha de preços e os documentos de imposto

Uma fatura mensal é gerada para cada perfil de cobrança em sua conta de cobrança. Para cada fatura, você pode baixar o arquivo CSV de uso e encargos do Azure, a planilha de preços e o documento de imposto (se aplicável). Você também pode baixar o arquivo CSV de uso e encargos do Azure para os encargos do mês atual.

Para saber como baixar o arquivo CSV de uso e encargos do Azure, confira [Baixar o uso de seu Contrato de Cliente da Microsoft](billing-download-azure-daily-usage.md).

Para saber como baixar a planilha de preços, confira [Baixar os preços de seu Contrato de Cliente da Microsoft](billing-ea-pricing.md).

Para saber como baixar os documentos de imposto confira [Exibir os documentos de imposto de seu Contrato de Cliente da Microsoft](billing-mca-download-tax-document.md#view-and-download-tax-documents).

### <a name="add-an-additional-enterprise-administrator"></a>Adicionar outro administrador corporativo

Permita aos usuários o acesso ao perfil de cobrança para permitir que eles exibam e gerenciem a cobrança de sua organização. Use a página **Controle de Acesso (IAM)** no portal do Azure para permitir acesso.  Para saber mais sobre as funções do perfil de cobrança, confira [Funções e tarefas do perfil de cobrança](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

Para saber como fornecer acesso ao seu perfil de cobrança, confira [Gerenciar funções de cobrança no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-department"></a>Criar um departamento

Crie uma seção da fatura para organizar os custos conforme suas necessidades, de maneira semelhante aos departamentos no registro do Contrato Enterprise. Crie uma seção da fatura no portal do Azure. Para saber mais, confira [Criar seções em sua fatura para organizar os custos](billing-mca-section-invoice.md).

### <a name="create-a-new-account"></a>Criar uma nova conta

Atribua aos usuários a função de criador de assinatura do Azure nas seções da fatura para conceder a eles a permissão para criar uma assinatura do Azure, como as contas criadas no registro do Contrato Enterprise. Para obter mais informações sobre como atribuir funções, confira [Gerenciar funções de cobrança no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

## <a name="changes-for-department-administrators"></a>Alterações para administradores do departamento

As alterações a seguir se aplicam aos administradores do departamento em um Contrato Enterprise que foi renovado para um Contrato de Cliente da Microsoft.

- Uma seção da fatura é criada para cada departamento no registro do Contrato Enterprise. Você usará as seções da fatura para gerenciar os departamentos. Para saber mais sobre as seções da fatura, confira [Entender as seções da fatura](billing-mca-overview.md#invoice-sections).
- Você usará a função de criador de assinatura do Azure na seção da fatura para conceder a outros usuários a permissão para criar uma assinatura do Azure, como as contas criadas no registro do Contrato Enterprise.
- Você usará o portal do Azure para gerenciar a cobrança de sua organização, em vez do portal do Azure EA.

Você recebe as seguintes funções na nova conta de cobrança:

**Proprietário da seção da fatura** – você recebe a função de proprietário da seção da fatura na seção da fatura criada para os departamentos existentes em seu registro do Contrato Enterprise. A função permite exibir e acompanhar os encargos, além de controlar quem pode criar assinaturas do Azure e comprar outros produtos para a seção da fatura.

### <a name="view-charges-for-your-department"></a>Exibir os encargos de seu departamento

Exiba os encargos para a seção da fatura criada para seu departamento na [página Gerenciamento de Custos + Cobrança](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) do portal do Azure.

### <a name="add-an-additional-department-administrator"></a>Adicionar outro administrador do departamento

Uma seção da fatura é criada para cada departamento que você tinha no Contrato Enterprise. Use a página **Controle de Acesso (IAM)** no portal do Azure para permitir a outros usuários o acesso para exibir e gerenciar a seção da fatura. Para saber mais sobre as funções da seção da fatura, confira [Funções e tarefas da seção da fatura](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Para saber como fornecer acesso à seção da fatura, confira [Gerenciar funções de cobrança no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

### <a name="create-a-new-account-in-your-department"></a>Criar uma conta em seu departamento

Atribua usuários à função de criador de assinatura do Azure na seção da fatura criada para seu departamento. Para obter mais informações sobre como atribuir funções, confira [Gerenciar funções de cobrança no portal do Azure](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="view-charges-for-accounts-in-your-departments"></a>Exibir encargos para contas em seus departamentos

Não há suporte na nova conta de cobrança para as contas que foram criadas no registro do Contrato Enterprise. As assinaturas da conta pertencem à respectiva seção da fatura de seus departamentos. Os proprietários da conta podem criar e gerenciar assinaturas para as seções da fatura.

Para exibir o custo agregado de assinaturas que pertenciam a uma conta em seu departamento, é necessário definir um centro de custo para cada assinatura. Em seguida, use o arquivo de uso e encargos do Azure para filtrar as assinaturas pelo centro de custo.

## <a name="changes-for-account-owners"></a>Alterações para proprietários da conta

Os proprietários da conta no Contrato Enterprise obtêm permissão para criar assinaturas do Azure na nova conta de cobrança. Suas assinaturas existentes do Azure pertencem à seção da fatura criada para seu departamento. Caso a sua conta não pertença a um departamento, suas assinaturas pertencerão a uma seção da fatura chamada Seção da fatura padrão.

Para criar assinaturas adicionais do Azure, você receberá a função a seguir na nova conta de cobrança.

**Criador de assinatura do Azure** – você recebe a função de criador de assinatura do Azure na seção da fatura criada para seu departamento no Contrato Enterprise. Caso a sua conta não pertença a um departamento, você receberá a função de criador de assinatura do Azure em uma seção da fatura chamada Seção da fatura padrão. A função permite que você crie assinaturas do Azure para a seção da fatura.

### <a name="create-an-azure-subscription"></a>Cria uma assinatura do Azure

Crie assinaturas do Azure para a seção da fatura no portal do Azure. Para obter mais informações, confira [Criar uma assinatura adicional do Azure para o Contrato de Cliente da Microsoft](billing-mca-create-subscription.md)

### <a name="view-charges-for-your-account"></a>Exibir os encargos de sua conta

Para exibir os encargos das assinaturas que pertenciam a uma conta, acesse a [página de assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure. A página de assinaturas exibe os encargos de todas as suas assinaturas.

### <a name="view-charges-for-a-subscription"></a>Exibir os encargos de uma assinatura

Exiba os encargos de uma assinatura na [página de assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou na análise de custo do Azure. Para obter mais informações sobre a análise de custo do Azure, confira [Explorar e analisar custos com a Análise de custo](../cost-management/quick-acm-cost-analysis.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

- [Entender a conta de cobrança para um Contrato de Cliente da Microsoft](billing-mca-overview.md)
- [Entender sua fatura](billing-understand-your-bill.md)
- [Entenda sua cobrança](billing-understand-your-invoice.md)
- [Obter a propriedade de cobrança das assinaturas do Azure de outros usuários](billing-mca-request-billing-ownership.md)
