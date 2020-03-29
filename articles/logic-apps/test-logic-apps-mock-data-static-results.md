---
title: Testar aplicativos lógicos usando dados fictícios
description: Configure resultados estáticos para testar aplicativos lógicos com dados simulados sem afetar ambientes de produção
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790272"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Teste aplicativos lógicos com dados simulados configurando resultados estáticos

Ao testar seus aplicativos lógicos, você pode não estar pronto para realmente ligar ou acessar aplicativos, serviços e sistemas por várias razões. Normalmente, nesses cenários, você pode ter que executar diferentes caminhos de condição, erros de força, fornecer corpos específicos de resposta a mensagens ou até mesmo tentar pular alguns passos. Ao configurar resultados estáticos para uma ação em seu aplicativo lógico, você pode simular dados de saída dessa ação. Ativar resultados estáticos em uma ação não executa a ação, mas retorna os dados simulados.

Por exemplo, se você configurar resultados estáticos para a ação de envio de e-mails do Outlook 365, o mecanismo Logic Apps apenas retorna os dados simulados especificados como resultados estáticos, em vez de ligar para o Outlook e enviar um e-mail.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico onde você deseja configurar resultados estáticos

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Configure resultados estáticos

1. Se você ainda não tem, no [portal Azure,](https://portal.azure.com)abra seu aplicativo lógico no Logic Apps Designer.

1. Na ação em que deseja configurar resultados estáticos, siga estas etapas: 

   1. No canto superior direito da ação, escolha o botão elipses (*...*) e selecione **o resultado estático,** por exemplo:

      ![Selecione "Resultado estático" > "Habilitar resultado estático"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Escolha **Ativar resultado estático**. Para as propriedades (*) necessárias, especifique os valores de saída simuladas que deseja retornar para a resposta da ação.

      Por exemplo, aqui estão as propriedades necessárias para a ação HTTP:

      | Propriedade | Descrição |
      |----------|-------------|
      | **Status** | O status da ação para retornar |
      | **Código de status** | O código de status específico para retornar |
      | **Cabeçalhos** | O conteúdo do cabeçalho para retornar |
      |||

      ![Selecione "Ativar resultado estático"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Para inserir os dados simulados no formato JSON (JavaScript Object Notation, notação de objeto](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)sinuoso), escolha **Alternar para o modo JSON** (Escolha!["Alternar para o Modo JSON").

   1. Para propriedades opcionais, abra a lista **Selecionar campos opcionais** e selecione as propriedades que deseja simular.

      ![Selecione propriedades opcionais](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Quando estiver pronto para salvar, escolha **Feito**.

   No canto superior direito da ação, a barra de título![mostra agora](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)um ícone de béquer de teste (Ícone para resultados estáticos), que indica que você habilitou resultados estáticos.

   ![Ícone mostrando resultados estáticos habilitados](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Para encontrar executores anteriores que usam dados simulados, consulte [Encontrar executões que usam resultados estáticos](#find-runs-mock-data) mais tarde neste tópico.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Reutilizar saídas anteriores

Se o seu aplicativo lógico tiver uma execução anterior com saídas que você pode reutilizar como saídas falsas, você pode copiar e colar as saídas dessa execução.

1. Se você ainda não tem, no [portal Azure,](https://portal.azure.com)abra seu aplicativo lógico no Logic Apps Designer.

1. No menu principal do aplicativo lógico, selecione **Visão geral**.

1. Na seção **Histórico De execução,** selecione a execução do aplicativo lógico desejada.

1. No fluxo de trabalho do seu aplicativo lógico, encontre e expanda a ação que tem as saídas desejadas.

1. Escolha o link **Mostrar saídas brutas.**

1. Copie o objeto JSON (Notação de objeto JavaScript completo) ou a subseção específica que você deseja usar, por exemplo, a seção saídas ou mesmo apenas a seção cabeçalhos.

1. Siga as etapas para abrir a caixa **de resultados estáticos** para sua ação em [Configurar resultados estáticos](#set-up-static-results).

1. Depois que a caixa **de resultados Estático** for aberta, escolha qualquer etapa:

   * Para colar um objeto JSON completo, escolha **Alternar para o modo JSON** (Escolha!["Alternar para o modo JSON"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Escolha "Alternar para o modo JSON" para objeto completo](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Para colar apenas uma seção JSON, ao lado do rótulo dessa seção, escolha **Alternar para o modo JSON** para essa seção, por exemplo:

     ![Escolha "Alternar para o modo JSON" para saídas](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. No editor JSON, cole seu JSON copiado anteriormente.

   ![Modo JSON](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Quando tiver terminado, escolha **Concluído**. Ou, para retornar ao designer, escolha![o Modo Editor](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png) **de Switch** (Escolha "Modo editor de switch").

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Encontre corridas que usam resultados estáticos

O histórico de executações do seu aplicativo lógico identifica as corridas onde as ações usam resultados estáticos. Para encontrar essas corridas, siga estas etapas:

1. No menu principal do aplicativo lógico, selecione **Visão geral**. 

1. No painel direito, em **Executa histórico,** encontre a coluna **Resultados Estáticos.** 

   Qualquer execução que inclua ações com resultados tem a coluna **Resultados Estáticos** definida **como Ativada,** por exemplo:

   ![Histórico de execução - coluna de resultados estáticos](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Para exibir ações que usam resultados estáticos, selecione a execução desejada onde a coluna **Resultados Estáticos** está definida **como Ativada**.

   As ações que usam resultados estáticos mostram o ícone do béquer de teste (Ícone![para resultados](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)estáticos), por exemplo:

   ![Histórico de execução - ações que usam resultados estáticos](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Desativar resultados estáticos

Desligar os resultados estáticos não joga fora os valores da sua última configuração. Assim, quando você ativar resultados estáticos da próxima vez, você pode continuar usando seus valores anteriores.

1. Encontre a ação onde deseja desativar saídas estáticas. No canto superior direito da ação, escolha o![ícone do](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)béquer de teste (Ícone para resultados estáticos).

   ![Desativar resultados estáticos](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Escolha **Desativar o resultado** > estático**feito**.

   ![Desativar resultados estáticos](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Referência

Para obter mais informações sobre essa configuração nas definições de fluxo de trabalho subjacentes, consulte [resultados estáticos - Referência de esquema para linguagem](../logic-apps/logic-apps-workflow-definition-language.md#static-results) de definição de fluxo de trabalho e tempo de [execuçãoConfiguração.estáticaResultado - Configurações de configuração em tempo de execução](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [os aplicativos azure logic](../logic-apps/logic-apps-overview.md)