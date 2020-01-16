---
title: Exibir suas contas de cobrança no portal do Azure | Microsoft Docs
description: Saiba como exibir suas contas de cobrança no portal do Azure.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 2768d6e146a37e86bb36353f661179ebd7b5033d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994123"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Contas de cobrança e escopos no portal do Azure

Uma conta de cobrança é criada quando você se inscreve para usar o Azure. Você usa a conta de cobrança para gerenciar faturas, pagamentos e acompanhar os custos. Você pode ter acesso a várias contas de cobrança. Por exemplo, você pode ter se inscrito no Azure para seus projetos pessoais. Você também pode ter acesso por meio do Contrato Enterprise de sua organização ou do Contrato de Cliente da Microsoft. Para cada um desses cenários, você terá uma conta de cobrança separada.

O portal do Azure dá suporte ao seguinte tipo de contas de cobrança:

- **Programa do Microsoft Online Services**: uma conta de cobrança para um programa do Microsoft Online Services é criada quando você se inscreve no Azure por meio do site do Azure. Por exemplo, quando você se inscreve em uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), uma [conta com taxas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise Agreement**: uma conta de cobrança para um Enterprise Agreement é criada quando sua organização assina um [ea (Enterprise Agreement)](https://azure.microsoft.com/pricing/enterprise-agreement/) para usar o Azure.

- **Contrato do cliente da Microsoft**: uma conta de cobrança para um contrato de cliente da Microsoft é criada quando sua organização trabalha com um representante da Microsoft para assinar um contrato de cliente da Microsoft. Alguns clientes em regiões selecionadas, que se inscrevem no site do Azure para uma [conta com tarifas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) também podem ter uma conta de cobrança para um contrato de cliente da Microsoft. Para obter mais informações, confira [Introdução à conta de cobrança para o Contrato de Cliente da Microsoft](../understand/mca-overview.md).

- **Contrato de parceiro da Microsoft**: uma conta de cobrança para um contrato de parceiro da Microsoft é criada para parceiros do CSP (provedor de soluções na nuvem) para gerenciar seus clientes na nova experiência de comércio. Os parceiros precisam ter pelo menos um cliente com um [plano do Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan) para gerenciar sua conta de cobrança na portal do Azure. Para obter mais informações, consulte Introdução [à sua conta de cobrança para o contrato de parceiro da Microsoft](../understand/mpa-overview.md).

Para determinar o tipo de sua conta de cobrança, confira [Verificar o tipo de sua conta de cobrança](#check-the-type-of-your-account).

## <a name="scopes-for-billing-accounts"></a>Escopos para contas de cobrança
Um escopo é um nó em uma conta de cobrança que você usa para exibir e gerenciar a cobrança. É onde você gerencia os dados de cobrança, pagamentos, faturas e realiza o gerenciamento geral da conta.

### <a name="microsoft-online-services-program"></a>Programa Microsoft Online Services

 ![Captura de tela que mostra a hierarquia MOSP](./media/view-all-accounts/mosp-hierarchy.png)

|Escopo  |Definição  |
|---------|---------|
|Conta de cobrança     | Representa um contrato que um cliente aceita para usar o Azure. Ele contém uma ou mais assinaturas.  |
|Subscription     |  Representa um agrupamento de recursos do Azure. A fatura é gerada nesse escopo. Outras informações de cobrança, como métodos de pagamento e endereço de uso, estão associadas a esse escopo.|

### <a name="enterprise-agreement"></a>Enterprise Agreement

![Captura de tela que mostra a hierarquia de EA](./media/view-all-accounts/ea-hierarchy.png)

|Escopo  |Definição  |
|---------|---------|
|Conta de cobrança    | Representa um registro do Contrato Enterprise. Ele contém um ou mais departamentos e contas. A fatura é gerada nesse escopo. |
|department     |  Agrupamento opcional de contas para segmentar custos em agrupamentos lógicos e definir orçamento.     |
|Conta     |  Representa um único proprietário da conta. Os proprietários da conta têm permissões para criar e gerenciar assinaturas do Azure que são cobradas para o registro. |

### <a name="microsoft-customer-agreement"></a>Contrato de Cliente Microsoft

![Captura de tela que mostra a hierarquia de MCA](./media/view-all-accounts/mca-hierarchy.png)

|Escopo  |Tarefas  |
|---------|---------|
|Conta de cobrança     |   Representa um contrato que um cliente aceita para usar produtos e serviços da Microsoft. Ele contém um ou mais perfis de cobrança. |
|Perfil de faturamento     |   Representa uma fatura e as informações de cobrança relacionadas, como métodos de pagamento e endereço de cobrança. Ele contém uma ou mais seções de fatura. |
|Seção da fatura     |   Representa um agrupamento de custos em uma fatura. As assinaturas do Azure e outras compras, como o Azure Marketplace e os produtos de origem do aplicativo, estão associadas a esse escopo.    |

### <a name="microsoft-partner-agreement"></a>Contrato de parceiro da Microsoft

![Captura de tela que mostra a hierarquia do MPA](./media/view-all-accounts/mpa-hierarchy.png)

|Escopo  |Tarefas  |
|---------|---------|
|Conta de cobrança     |   Representa um contrato de parceiro para gerenciar os produtos e serviços da Microsoft dos clientes na nova experiência de comércio. Ele contém um ou mais perfis e clientes de cobrança.   |
|Perfil de faturamento     |   Representa uma nota fiscal de uma moeda.     |
|Cliente    |   Representa um cliente para um parceiro CSP (provedor de soluções na nuvem).  As assinaturas do Azure e outras compras, como o Azure Marketplace e os produtos de origem do aplicativo, estão associadas a esse escopo.  |
|Revendedor    |   Revendedor que fornece serviços a um cliente. É um campo opcional para uma assinatura e é aplicável somente para provedores indiretos no modelo de duas camadas do CSP.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Alternar o escopo do orçamento no portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. Na página Visão geral, selecione **alternar escopo**.

   ![Captura de tela que mostra escopos de cobrança](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Você não verá o escopo do comutador se você só tiver acesso a um escopo.

4. Selecione um escopo para exibir detalhes.

   ![Captura de tela que mostra escopos de cobrança](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Verifique o tipo de sua conta
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximos passos
- Saiba como começar a [analisar os custos](../costs/quick-acm-cost-analysis.md).
