---
title: Conectar ao GitHub
description: Monitorar eventos do GitHub com as APIs REST do GitHub e os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 7dc865f520b6f4667ace720e656a210e0252d1a1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789766"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Conectar-se ao GitHub de aplicativos lógicos do Azure

O GitHub é um serviço de hospedagem de repositório Git baseado na Web que oferece toda a funcionalidade de controle de revisão distribuída e SCM (gerenciamento código-fonte) do Git mais outros recursos.

Para começar com o conector do GitHub, [primeiro crie um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Criar uma conexão com o GitHub

Para usar o conector do GitHub em um aplicativo lógico, é necessário primeiro criar uma *conexão* e, em seguida, fornecer detalhes sobre essas propriedades: 

| Propriedade | obrigatórios | Descrição | 
| -------- | -------- | ----------- | 
| restrição | SIM | Forneça as credenciais do GitHub. |

Após criar a conexão, será possível executar as ações e ouvir os gatilhos descritos neste artigo.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência do conector](/connectors/github/).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)