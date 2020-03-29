---
title: Acesse, monitore e gerencie seu repo do GitHub
description: Monitore os eventos do GitHub e gerencie seu repo do GitHub criando fluxos de trabalho automatizados com aplicativos azure logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378442"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Monitore e gerencie seu repo do GitHub usando aplicativos azure logic

O GitHub é um serviço de hospedagem de repositório Git baseado na Web que oferece toda a funcionalidade de controle de revisão distribuída e SCM (gerenciamento código-fonte) do Git mais outros recursos.

Para começar com o conector do GitHub, [primeiro crie um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Criar uma conexão com o GitHub

Para usar o conector do GitHub em um aplicativo lógico, é necessário primeiro criar uma *conexão* e, em seguida, fornecer detalhes sobre essas propriedades: 

| Propriedade | Obrigatório | Descrição | 
| -------- | -------- | ----------- | 
| Token | Sim | Forneça as credenciais do GitHub. |

Após criar a conexão, será possível executar as ações e ouvir os gatilhos descritos neste artigo.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição OpenAPI (anteriormente Swagger) do conector, revise a [página de referência do conector](/connectors/github/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)