---
title: Validar XML para integração corporativa B2B
description: Valide o XML usando schemas em aplicativos azure logic com pacote de integração corporativa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792170"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Validar XML para Enterprise Integration B2B nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack

Muitas vezes, em cenários B2B, os parceiros comerciais em um acordo precisam ter certeza de que as mensagens que eles trocam são válidas antes que qualquer processamento de dados possa começar. Você pode validar documentos contra um esquema predefinido usando a ação de validação XML, que está disponível com o Enterprise Integration Pack.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você ainda não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um aplicativo lógico em branco ou existente onde você deseja usar a ação de validação XML. Se você não estiver familiarizado com os Aplicativos Lógicos, examine [O que são Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início rápido: crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associada à sua assinatura do Azure, está vinculada ao aplicativo lógico onde você planeja usar a ação de validação XML e contém o esquema que deseja usar para validar o conteúdo XML. Tanto seu aplicativo lógico quanto sua conta de integração devem existir no mesmo local ou região do Azure.

## <a name="add-xml-validation-action"></a>Adicionar ação de validação XML

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. Se você tiver um aplicativo de lógica em branco, no `HTTP request` Logic App Designer, na caixa de pesquisa, digite como seu filtro e selecione o **gatilho Quando uma solicitação HTTP for recebida.** Caso contrário, prossiga para a próxima etapa.

1. Na última etapa do seu fluxo de trabalho, selecione **Nova etapa**.

   Para adicionar uma ação entre as etapas existentes, mova o ponteiro sobre a**+** seta que conecta essas etapas para que o sinal de mais () apareça. Selecione esse sinal de mais e selecione **Adicionar uma ação**.

1. Em **Escolha uma ação**, selecione **Interno**. Na caixa de pesquisa, insira `xml validation` como o filtro. Na lista de ações, selecione **Validação XML**.

   ![Encontre e selecione a ação "Validação XML"](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Para especificar o conteúdo XML que deseja validar, clique na caixa **Conteúdo** para que a lista de conteúdo dinâmico seja exibida.

   ![Lista de conteúdo dinâmico aberto](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   A lista de conteúdo dinâmico mostra tokens de propriedade que representam as saídas das etapas anteriores no fluxo de trabalho. Se a lista não mostrar uma propriedade esperada, verifique o gatilho ou o título de ação se você pode selecionar **Ver mais**.

1. Na lista de conteúdo dinâmico, selecione a propriedade que tem o conteúdo que deseja validar.

   Este exemplo seleciona a saída **do corpo** do gatilho.

   ![Selecione o conteúdo para validar](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Para especificar o esquema que deseja usar para validação, abra a lista **Nome de Esquema** e selecione o esquema de validação que você adicionou à sua conta de integração vinculada.

   ![Selecione esquema para usar para validação](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Salve seu aplicativo lógico.

   Agora você terminou de configurar sua validação. Em um aplicativo do mundo real, você pode querer armazenar os dados validados em um aplicativo de linha de negócios (LOB), como o SalesForce. Para enviar a saída validada ao Salesforce, adicione uma ação.

1. Para testar sua ação de validação, você pode enviar uma solicitação para ativar o fluxo de trabalho do seu aplicativo lógico.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md)