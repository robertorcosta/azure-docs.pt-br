---
title: Gerencie dados, planilhas e tabelas no Excel Online
description: Gerencie dados em planilhas e tabelas no Excel Online for Business ou Excel Online para OneDrive usando aplicativos azure logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: b1df1219590aa94331a4404b1315eb7231c59699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445877"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Gerenciar dados do Excel Online com Aplicativos Lógicos do Azure

Com [o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o [Conector Excel Online for Business](/connectors/excelonlinebusiness/) ou excel online para conector [OneDrive,](/connectors/excelonline/) você pode criar tarefas e fluxos de trabalho automatizados com base em seus dados no Excel Online for Business ou OneDrive. Esse conector fornece ações que ajudam você a trabalhar com seus dados e gerenciar planilhas, por exemplo:

* Crie novas planilhas e tabelas.
* Obter e gerenciar planilhas, tabelas e linhas.
* Adicionar linhas únicas e colunas de chave.

Em seguida, você pode usar as saídas dessas ações com ações para outros serviços. Por exemplo, se você usa uma ação que cria planilhas a cada semana, você pode usar outra ação que envia email de confirmação por meio do conector do Office 365 Outlook.

Se você é novo em aplicativos lógicos, [revise o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Os conectores do [Excel Online for Business](/connectors/excelonlinebusiness/) e do [Excel Online for OneDrive](/connectors/excelonline/) funcionam com os Aplicativos Lógicos do Azure e diferem do [conector do Excel para o PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta do Office 365](https://www.office.com/) para sua conta corporativa ou conta pessoal Microsoft

  Os dados do Excel podem existir como um arquivo CSV (valores separados por vírgulas) em uma pasta de armazenamento, por exemplo, no OneDrive. 
  Você também pode usar o mesmo arquivo CSV com o [conector de arquivo simples](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar os dados do Excel Online. Esse conector fornece apenas ações, portanto, para iniciar seu aplicativo lógico, selecione um gatilho separado, por exemplo, o gatilho **Recorrência**.

## <a name="add-excel-action"></a>Adicionar ação de Excel

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer de aplicativo lógico, se já não estiver aberto.

1. No gatilho, escolha **Nova etapa**.

1. Na caixa de pesquisa, insira "excel" como filtro. Na lista de ações, selecione a ação desejada.

   > [!NOTE]
   > O Logic App Designer não pode carregar tabelas com 100 ou mais colunas. Se possível, reduza o número de colunas na tabela selecionada para que o designer possa carregar a tabela.

1. Se solicitado, faça login na sua conta do Office 365.

   Suas credenciais autorizam o aplicativo lógico a criar uma conexão com o Excel Online e acessar seus dados.

1. Continue fornecendo os detalhes necessários para a ação selecionada, criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelos arquivos OpenAPI (anteriormente Swagger) do conector, consulte essas páginas de referência do conector:

* [Excel Online for Business](/connectors/excelonlinebusiness/)
* [Excel Online for OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
