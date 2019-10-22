---
title: Crie uma assinatura adicional do Azure|Microsoft Docs
description: Saiba como adicionar uma nova assinatura do Azure no portal do Azure.
services: billing
documentationcenter: ''
author: amberb
manager: amberb
editor: ''
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: banders
ms.openlocfilehash: acb41de8344d1467e86c38c7c99e8ad4ab517ead
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375821"
---
# <a name="create-an-additional-azure-subscription"></a>Criar uma assinatura adicional do Azure

Crie uma assinatura adicional para a sua conta de cobrança do [EA (Contrato Enterprise)](https://azure.microsoft.com/pricing/enterprise-agreement/), do [Contrato de Cliente da Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) ou do [Contrato de Parceiro da Microsoft](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) no portal do Azure. O ideal é ter uma assinatura adicional para evitar atingir os limites da assinatura, criar ambientes separados para segurança ou isolar os dados por motivos de conformidade.

Caso tenha uma conta de cobrança do MOSP (Programa do Microsoft Online Services), você poderá criar assinaturas adicionais no [portal de inscrição do Azure](https://account.azure.com/signup?offer=ms-azr-0003p).

Para saber mais sobre contas de cobrança e identificar o tipo de sua conta de cobrança, confira [Exibir contas de cobrança no portal do Azure](billing-view-all-accounts.md).

## <a name="permission-required-to-create-azure-subscriptions"></a>Permissão necessária para criar assinaturas do Azure

Você precisa das seguintes permissões para criar assinaturas:

|Conta de cobrança  |Permissão  |
|---------|---------|
|EA (Enterprise Agreement) |  Função proprietário da conta no registro do Contrato Enterprise. Para obter mais informações, consulte [Entendendo as funções administrativas do Azure Enterprise Agreement no Azure](billing-understand-ea-roles.md).    |
|MCA (Contrato de Cliente da Microsoft) |  Função proprietário ou colaborador na seção da fatura, no perfil de cobrança ou na conta de cobrança. Ou a função criador de assinatura do Azure na seção da fatura.  Para obter mais informações, confira [Funções e tarefa da cobrança de assinatura](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).    |
|MPA (Contrato de Parceiro da Microsoft) |   Função Administrador Global e Agente Administrativo na organização do parceiro. Para saber mais, confira [Partner Center – Atribuir funções e permissões de usuários](https://docs.microsoft.com/partner-center/permissions-overview)  |

## <a name="create-a-subscription-in-the-azure-portal"></a>Criar uma assinatura no portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Pesquise **Assinaturas**.

   ![Captura de tela que mostra a pesquisa no portal para assinatura](./media/billing-create-subscription/billing-search-subscription-portal.png)

1. Selecione **Adicionar**.

   ![Captura de tela que mostra o botão Adicionar na exibição Assinaturas](./media/billing-create-subscription/subscription-add.png)

1. Caso tenha acesso a várias contas de cobrança, selecione a conta de cobrança para a qual deseja criar a assinatura.

1. Preencha o formulário e clique em **Criar**. As tabelas abaixo listam os campos no formulário para cada tipo de conta de cobrança.

**Contrato Enterprise**

|Campo  |Definição  |
|---------|---------|
|NOME     | O nome de exibição que ajuda você a identificar com facilidade a assinatura no portal do Azure.  |
|Oferta     | Selecione Desenvolvimento/Teste do EA se pretender usar essa assinatura para cargas de trabalho de desenvolvimento ou teste, caso contrário, use Microsoft Azure Enterprise. A oferta de DevTest precisa estar habilitada para sua conta de registro para criar assinaturas de Desenvolvimento/Teste do EA.|

**Contrato de Cliente da Microsoft**

|Campo  |Definição  |
|---------|---------|
|Perfil de faturamento     | Os encargos de sua assinatura serão cobrados no perfil de cobrança selecionado. Caso você tenha acesso a apenas um perfil de cobrança, a seleção ficará esmaecida.     |
|Seção da fatura     | Os encargos de sua assinatura serão exibidos nessa seção da fatura do perfil de cobrança. Caso você tenha acesso a apenas uma seção da fatura, a seleção ficará esmaecida.  |
|Plano     | Selecione Plano do Microsoft Azure para DevTest se pretender usar essa assinatura para cargas de trabalho de desenvolvimento ou teste, caso contrário, use Plano do Microsoft Azure. Se apenas um plano estiver habilitado para o perfil de cobrança, a seleção ficará esmaecida.  |
|NOME     | O nome de exibição que ajuda você a identificar com facilidade a assinatura no portal do Azure.  |

**Contrato de Parceiro da Microsoft**

|Campo  |Definição  |
|---------|---------|
|Cliente    | A assinatura é criada para o cliente selecionado. Caso tenha apenas um cliente, a seleção ficará esmaecida.  |
|Revendedor    | O revendedor que fornecerá serviços ao cliente. Esse é um campo opcional, que só é aplicável aos provedores indiretos no modelo de duas camadas do CSP. |
|NOME     | O nome de exibição que ajuda você a identificar com facilidade a assinatura no portal do Azure.  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>Criar uma assinatura adicional do Azure de forma programática

Você também pode criar assinaturas adicionais programaticamente. Para obter mais informações, confira [Criar assinaturas do Azure de forma programática](../azure-resource-manager/programmatically-create-subscription.md).

## <a name="next-steps"></a>Próximas etapas

- [Adicionar ou alterar administradores de assinatura do Azure](billing-add-change-azure-subscription-administrator.md)
- [Mover recursos para um novo grupo de recursos ou uma nova assinatura](../azure-resource-manager/resource-group-move-resources.md?toc=/azure/billing/TOC.json)
- [Crie grupos de gerenciamento para organização e gerenciamento de recursos](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
- [Cancelar sua assinatura do Azure](billing-how-to-cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
