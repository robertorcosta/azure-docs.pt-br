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
ms.openlocfilehash: ec3236ffba409c390a12e46dd29263b18857c647
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74222725"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Exibir as contas de cobrança no portal do Azure  

Uma conta de cobrança é criada quando você se inscreve para usar o Azure. Você usa a conta de cobrança para gerenciar faturas, pagamentos e acompanhar os custos. Você pode ter acesso a várias contas de cobrança. Por exemplo, você pode ter se inscrito no Azure para seus projetos pessoais. Você também pode ter acesso por meio do Contrato Enterprise de sua organização ou do Contrato de Cliente da Microsoft. Para cada um desses cenários, você terá uma conta de cobrança separada.

Atualmente, o portal do Azure dá suporte aos seguintes tipos de contas de cobrança:

- **Programa Microsoft Online Services**: Uma conta de cobrança para um Programa Microsoft Online Services é criada quando você se inscreve no Azure por meio do site do Azure. Por exemplo, quando você se inscreve em uma [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), uma [conta com taxas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Contrato Enterprise**: Uma conta de cobrança para um Contrato Enterprise é criada quando sua organização assina um [EA (Contrato Enterprise)](https://azure.microsoft.com/pricing/enterprise-agreement/) para usar o Azure.

- **Contrato de Cliente da Microsoft**: Uma conta de cobrança para um Contrato de Cliente da Microsoft é criada quando sua organização decide assinar um Contrato de Cliente da Microsoft por meio de um representante da Microsoft. Alguns clientes em regiões selecionadas, que se inscrevem por meio do site do Azure em uma [conta com taxas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou atualizam sua [conta gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), também podem ter uma conta de cobrança para um Contrato de Cliente da Microsoft. Para obter mais informações, confira [Introdução à conta de cobrança para o Contrato de Cliente da Microsoft](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Escopos para contas de cobrança
Um escopo é um nó em uma conta de cobrança que os usuários usam para exibir e gerenciar a cobrança. É nele que os usuários gerenciam dados de cobrança, pagamentos, faturas e realizam o gerenciamento geral da conta. 

### <a name="microsoft-online-services-program"></a>Programa Microsoft Online Services

|Escopo  |Definição  |
|---------|---------|
|Conta de cobrança     | Representa um único proprietário (administrador da conta) de uma ou mais assinaturas do Azure. Um administrador da conta está autorizado a executar várias tarefas de cobrança, como criar assinaturas, exibir faturas ou alterar a cobrança de assinaturas.  |
|Subscription     |  Representa um agrupamento de recursos do Azure. A fatura é gerada nesse escopo. Ela tem suas próprias formas de pagamento que são usadas para pagar a fatura.|


### <a name="enterprise-agreement"></a>Contrato Enterprise

|Escopo  |Definição  |
|---------|---------|
|Conta de cobrança    | Representa um registro do Contrato Enterprise. A fatura é gerada nesse escopo. Ela é estruturada usando departamentos e contas de registro.  |
|department     |  Agrupamento opcional de contas de registro.      |
|Conta de registro     |  Representa um único proprietário da conta. As assinaturas do Azure são criadas nesse escopo.  |


### <a name="microsoft-customer-agreement"></a>Contrato de Cliente da Microsoft

|Escopo  |Tarefas  |
|---------|---------|
|Conta de cobrança     |   Representa um contrato de cliente para vários produtos e serviços da Microsoft. Ela é estruturada usando perfis de cobrança e seções da fatura.   |
|Perfil de faturamento     |  Representa uma fatura e suas formas de pagamento. A fatura é gerada nesse escopo. Ele pode ter várias seções da fatura.      |
|Seção da fatura     |   Representa um grupo de custos em uma fatura. As assinaturas e outras compras estão associadas a esse escopo.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Alternar o escopo do orçamento no portal do Azure


1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Selecione **Todos os escopos do orçamento** do lado esquerdo.

   ![Captura de tela que mostra todos os escopos do orçamento](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** Você não verá **Todos os escopos do orçamento** se só tiver acesso a um escopo.

4. Selecione um escopo para exibir detalhes.



## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas
- Saiba como começar a [analisar os custos](../cost-management/quick-acm-cost-analysis.md).