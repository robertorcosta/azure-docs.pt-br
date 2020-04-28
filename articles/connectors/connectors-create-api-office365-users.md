---
title: Conectar-se aos usuários do Office 365
description: Automatizar tarefas e fluxos de trabalho que obtêm e gerenciam perfis em perfis de usuários do Office 365 usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75666849"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Obter e gerenciar perfis em usuários do Office 365 usando aplicativos lógicos do Azure

Conecte-se aos Usuários do Office 365 para obter perfis, pesquisar usuários e muito mais. Com os Usuários do Office 365, você pode:

* Criar seu fluxo de negócios baseado nos dados obtidos dos Usuários do Office 365. 
* Usar ações que obtêm relatórios diretos, o perfil de usuário de um gerenciador e muito mais. Essas ações obtém uma resposta e disponibilizam a saída para outras ações. Por exemplo, obtenha relatórios diretos de uma pessoa e, em seguida, utilize essas informações e atualize um banco de dados SQL Azure. 

É possível começar criando um aplicativo lógico agora. Consulte [Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Criar uma conexão com os Usuários do Office 365

Ao adicionar esse conector aos seus aplicativos lógicos, você deve entrar na sua conta de usuários do Office 365 para que os aplicativos lógicos do Azure possam se conectar à sua conta.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Depois de criar a conexão, insira as propriedades dos Usuários do Office 365, como a ID de usuário. A **referência da API REST** neste artigo descreve essas propriedades.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição do Swagger do conector, examine a [página de referência do conector](/connectors/officeusers/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](apis-list.md)