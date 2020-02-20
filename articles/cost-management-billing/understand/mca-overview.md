---
title: Introdução à cobrança do Contrato de Cliente da Microsoft – Azure
description: Entenda sua conta de cobrança do Contrato de Cliente da Microsoft
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: a580dd1fb9d2965a9da1e4918733bd5cf137e443
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199204"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Introdução à conta de cobrança do Contrato de Cliente da Microsoft

Uma conta de cobrança é criada quando você se inscreve para usar o Azure. Você usa a conta de cobrança para gerenciar faturas, pagamentos e acompanhar os custos. Você pode ter acesso a várias contas de cobrança. Por exemplo, você pode ter se inscrito no Azure para seus projetos pessoais. Você também pode ter acesso ao Azure por meio do Contrato Enterprise de sua organização ou do Contrato de Cliente da Microsoft. Para cada um desses cenários, você terá uma conta de cobrança separada.

Este artigo aplica-se a uma conta de cobrança para um Contrato de Cliente da Microsoft. [Verifique se você tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>Sua conta de cobrança

Sua conta de cobrança para o Contrato de Cliente da Microsoft contém um ou mais perfis de cobrança que lhe permitem gerenciar suas faturas e formas de pagamento. Cada perfil de cobrança contém uma ou mais seções de fatura que permitem organizar os custos na fatura do perfil de cobrança.

O diagrama a seguir mostra o relacionamento entre uma conta de cobrança, os perfis de cobrança e as seções da fatura.

![Diagrama mostrando a hierarquia de cobrança do Contrato de Cliente da Microsoft](./media/mca-overview/mca-billing-hierarchy.png)

As funções na conta de cobrança têm o nível mais alto de permissões. Por padrão, somente o usuário que se inscreveu no Azure tem acesso à conta de cobrança. Essas funções devem ser atribuídas a usuários que precisam exibir faturas e controlar os custos de toda a organização, como gerentes de finanças ou de TI. Para saber mais, confira [funções e tarefas da conta de cobrança](../manage/understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>Perfis de cobrança

Use um perfil de cobrança para gerenciar suas faturas e formas de pagamento. Uma fatura mensal é gerada no início do mês para cada perfil de cobrança de sua conta. A fatura contém os encargos respectivos a todas as assinaturas do Azure e a outras compras do mês anterior.

Um perfil de cobrança é criado automaticamente para sua conta de cobrança. Ele contém uma seção de fatura por padrão. Você pode criar seções adicionais para acompanhar e organizar custos com facilidade com base em suas necessidades, seja por projeto, departamento ou ambiente de desenvolvimento. Você verá essas seções na fatura do perfil de cobrança, refletindo o uso de cada assinatura e as compras que você atribuiu a ela.

As funções nos perfis de cobrança têm permissões para exibir e gerenciar faturas e métodos de pagamento. Atribua essas funções a usuários que pagam faturas, como membros da equipe de contabilidade de sua organização. Para saber mais, confira [funções e tarefas do perfil de cobrança](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Cada perfil de cobrança recebe uma fatura mensal

Uma fatura mensal é gerada no início do mês para cada perfil de cobrança. A fatura contém todos os encargos do mês anterior.

Você pode exibir a fatura, baixar documentos e alterar a configuração para receber faturas futuras por email no portal do Azure. Para obter mais informações, confira [Baixar faturas de um Contrato de Cliente da Microsoft](../manage/download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Formas de pagamento da fatura

Cada perfil de cobrança tem suas próprias formas de pagamento, usadas para pagar a fatura. As seguintes formas de pagamento têm suporte:

| Type             | Definição  |
|------------------|-------------|
|Créditos do Azure    |  Os créditos são aplicados automaticamente aos encargos qualificados em sua fatura, reduzindo o valor que você precisa pagar. Para saber mais, confira [Acompanhar o saldo de crédito do Azure para seu perfil de cobrança](../manage/mca-check-azure-credits-balance.md). |
|Cheque/transferência eletrônica | Se a conta for aprovada para pagamento por meio de cheque/transferência eletrônica. Você pode pagar o valor devido em sua fatura por cheque/transferência eletrônica. As instruções de pagamento são fornecidas na fatura |
|Cartão de crédito | Clientes que se inscreverem no Azure por meio do site do Azure podem pagar com cartão de crédito. |

### <a name="apply-policies-to-control-purchases"></a>Aplicar políticas para controlar compras

Aplique políticas para controlar compras do Azure Marketplace e de Reserva usando um perfil de cobrança. Você pode configurar políticas para desabilitar a compra de Reservas do Azure e de produtos do Marketplace. Quando as políticas são aplicadas, as assinaturas cobradas no perfil de cobrança não podem ser usadas para fazer essas compras.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Os planos do Azure determinam o preço e o contrato de nível de serviço das assinaturas

Os planos do Azure determinam o preço e o contrato de nível de serviço das assinaturas do Azure. Eles são habilitados automaticamente quando você cria um perfil de cobrança. Todas as seções da fatura associadas ao perfil de cobrança podem usar esses planos. Os usuários com acesso à seção de fatura usam os planos para criar assinaturas do Azure. Os seguintes planos do Azure têm suporte em contas de cobrança com o Contrato de Cliente da Microsoft:

| Plano             | Definição  |
|------------------|-------------|
|Plano do Microsoft Azure   | Permite que os usuários criem assinaturas que podem executar qualquer carga de trabalho.  |
|Plano do Microsoft Azure para Desenvolvimento/Teste | Permite que os assinantes do Visual Studio criem assinaturas restritas para cargas de trabalho de desenvolvimento ou teste. Essas assinaturas têm benefícios como taxas mais baixas e acesso a imagens de máquina virtual exclusivas no portal do Azure. |

## <a name="invoice-sections"></a>Seções da fatura

Crie seções da fatura para organizar os custos em sua fatura. Por exemplo, você pode precisar de uma única fatura para sua organização, mas deseja organizar os custos por departamento, equipe ou projeto. Para este cenário, você tem um único perfil de cobrança no qual cria uma seção de fatura para cada departamento, equipe ou projeto.

Quando uma seção de fatura é criada, você pode conceder a outras pessoas permissões para criar assinaturas do Azure que são cobradas na seção. Todas as compras e todos os encargos de uso das assinaturas são então faturados na seção.

As funções na seção da fatura têm permissões para controlar quem cria assinaturas do Azure. Atribua essas funções a usuários que configuram o ambiente do Azure para equipes em sua organização, como clientes potenciais de engenharia e arquitetos técnicos. Para obter mais informações, confira [funções e tarefas da seção da fatura](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos para saber mais sobre a sua conta de cobrança:

- [Entender as funções administrativas do Contrato de Cliente da Microsoft no Azure](../manage/understand-mca-roles.md)
- [Criar uma assinatura adicional do Azure para o Contrato de Cliente da Microsoft](../manage/create-subscription.md)
- [Criar seções em sua fatura para organizar os custos](../manage/mca-section-invoice.md)
