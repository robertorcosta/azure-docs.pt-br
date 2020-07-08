---
title: Conectar ao box
description: Automatizar tarefas e fluxos de trabalho que criam e gerenciam arquivos no box usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: c7f97ff33742eb545cbfbd7521ba135584851e5e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75666764"
---
# <a name="create-and-manage-files-in-box-by-using-azure-logic-apps"></a>Criar e gerenciar arquivos no box usando aplicativos lógicos do Azure

Este artigo mostra como você pode criar e gerenciar seus arquivos no Box de dentro de um aplicativo lógico com o conector do Box. Dessa forma, você pode criar aplicativos lógicos que automatizam tarefas e fluxos de trabalho para gerenciar seus arquivos e outras ações, por exemplo:

* Compile seu fluxo de negócios baseado nos dados que obtém do Box.

* Dispare o fluxo de trabalho e tarefas automatizadas quando um arquivo é criado ou atualizado.

* Executar uma ação que copia um arquivo ou exclui um arquivo.

  Quando essas ações obtêm uma resposta, elas disponibilizam a saída para outras ações. 
  Por exemplo, quando um arquivo é alterado no Box, você pode enviar esse arquivo em email usando o Office 365.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta do Box](https://www.box.com/home)

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* O aplicativo lógico no qual você deseja acessar a conta do Box. Para iniciar seu aplicativo lógico com um gatilho do Box, é necessário um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo OpenAPI (anteriormente Swagger) do conector, consulte a [página de referência do conector](/connectors/box/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)