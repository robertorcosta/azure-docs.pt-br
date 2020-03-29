---
title: Acordos de parceiros comerciais
description: Crie e gerencie acordos entre parceiros comerciais usando o Azure Logic Apps e o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 521a0ef4053be55e6c7322da5af26ccfc6c844e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790730"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Crie e gerencie acordos de parceiros comerciais em Aplicativos Azure Logic

Um*acordo* [de parceiro comercial](../logic-apps/logic-apps-enterprise-integration-partners.md) 
ajuda organizações e empresas a se comunicarem perfeitamente entre si, definindo o protocolo específico padrão do setor para usar ao trocar mensagens de negócios para negócios (B2B). Os acordos fornecem benefícios comuns, por exemplo:

* Permitir que as organizações troquem informações usando um formato bem conhecido.
* Melhore a eficiência ao realizar transações B2B.
* São fáceis de criar, gerenciar e usar para construir soluções de integração empresarial.

Este artigo mostra como criar um contrato AS2, EDIFACT ou X12 que você pode usar ao construir soluções de integração corporativa para cenários B2B usando o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) e o [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Depois de criar um contrato, você pode usar os conectores AS2, EDIFACT ou X12 para trocar mensagens B2B.

Para criar acordos para trocar mensagens do RosettaNet, consulte [Exchange Mensagens RosettaNet](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) para armazenar seu acordo e outros artefatos B2B. Essa conta de integração deve estar associada à sua assinatura do Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que você já criou em sua conta de integração. Um acordo requer tanto um parceiro anfitrião quanto um parceiro convidado. Ambos os parceiros devem usar o mesmo qualificador de "identidade de negócios" que o acordo que você deseja criar, como AS2, X12 ou EDIFACT.

* Opcional: O aplicativo lógico onde você deseja usar seu contrato e um gatilho que inicia o fluxo de trabalho do seu aplicativo lógico. Para apenas criar sua conta de integração e artefatos B2B, você não precisa de um aplicativo lógico. No entanto, antes que seu aplicativo lógico possa usar os artefatos B2B em sua conta de integração, você deve vincular sua conta de integração ao seu aplicativo lógico. Se você não estiver familiarizado com os Aplicativos Lógicos, examine [O que são Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início rápido: crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Criar contratos

1. Faça login no [portal Azure](https://portal.azure.com).
No menu principal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, digite "integração" como seu filtro. A partir dos resultados, selecione este recurso: **Contas de integração**

   ![Localizar sua conta de integração](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. Em **Contas integração,** selecione a conta de integração onde deseja criar o contrato.

   ![Selecione a conta de integração em que o contrato deve ser criado](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. No painel direito, em **Componentes,** escolha o azulejo **Agreements.**

   ![Escolha "Acordos"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. Em **Contratos**, escolha **Adicionar**. No **painel Adicionar,** forneça informações sobre o seu acordo, por exemplo:

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | <*nome do acordo*> | O nome do seu acordo |
   | **Tipo de acordo** | Sim | **AS2,** **X12**ou **EDIFACT** | O tipo de protocolo para o seu acordo. Quando você cria seu arquivo de contrato, o conteúdo nesse arquivo deve corresponder ao tipo de contrato. | |  
   | **Parceiro de Host** | Sim | <*host-partner-nome*> | O parceiro anfitrião representa a organização que especifica o acordo |
   | **Identidade do Host** | Sim | <*host-partner-identificador*> | O identificador do parceiro host |
   | **Parceiro Convidado** | Sim | <*nome convidado-parceiro*> | O parceiro convidado representa a organização que está fazendo negócios com o parceiro host |
   | **Identidade do Convidado** | Sim | <*convidado-parceiro-identificador*> | O identificador do parceiro convidado |
   | **Configurações de Recebimento** | Varia | Varia | Essas propriedades especificam como o parceiro host recebe todas as mensagens recebidas do parceiro convidado no contrato. Para obter mais informações, consulte o respectivo tipo de acordo: <p>- [Configurações de mensagem AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Configurações de mensagem EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Configurações de mensagem X12](logic-apps-enterprise-integration-x12.md) |
   | **Configurações de Envio** | Varia | Varia | Essas propriedades especificam como o parceiro host envia todas as mensagens de saída para o parceiro convidado no contrato. Para obter mais informações, consulte o respectivo tipo de acordo: <p>- [Configurações de mensagem AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Configurações de mensagem EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Configurações de mensagem X12](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Quando terminar de criar seu contrato, na página **Adicionar,** escolha **OK**e retorne à sua conta de integração.

   A lista **de acordos** agora mostra seu novo acordo.

## <a name="edit-agreements"></a>Editar acordos

1. No [portal Azure](https://portal.azure.com), no menu principal do Azure, selecione **Todos os serviços**.

1. Na caixa de pesquisa, digite "integração" como seu filtro. A partir dos resultados, selecione este recurso: **Contas de integração**

1. Em **Contas integração,** selecione a conta de integração que tem o contrato que deseja editar.

1. No painel direito, em **Componentes,** escolha o azulejo **Agreements.**

1. Em **Acordos,** selecione seu contrato e escolha **Editar**.

1. Faça e guarde suas mudanças.

## <a name="delete-agreements"></a>Excluir acordos

1. No [portal Azure](https://portal.azure.com), no menu principal do Azure, selecione **Todos os serviços**.

1. Na caixa de pesquisa, digite "integração" como seu filtro. A partir dos resultados, selecione este recurso: **Contas de integração**

1. Em **Contas integração,** selecione a conta de integração que tem o contrato que deseja excluir.

1. No painel direito, em **Componentes,** escolha o azulejo **Agreements.**

1. Em **Acordos,** selecione seu contrato e escolha **Excluir**.

1. Confirme que você realmente deseja excluir o contrato selecionado.

## <a name="next-steps"></a>Próximas etapas

* [Trocar mensagens AS2](logic-apps-enterprise-integration-as2.md)
* [Trocar mensagens EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Trocar mensagens X12](logic-apps-enterprise-integration-x12.md)
