---
title: Conectar-se ao Dropbox
description: Automatize tarefas e fluxos de trabalho que carregam e gerenciam arquivos no Dropbox usando aplicativos azure logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665744"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Carregar e gerenciar arquivos no Dropbox usando aplicativos azure logic

Com o conector Dropbox e o Azure Logic Apps, você pode criar fluxos de trabalho automatizados que carregam e gerenciam arquivos em sua conta dropbox. 

Este artigo mostra como se conectar ao Dropbox a partir do seu aplicativo lógico e, em seguida, adicionar o Dropbox **Quando um arquivo é criado** gatilho e o conteúdo do arquivo Dropbox Obter usando ação de **caminho.**

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta dropbox](https://www.dropbox.com/), que você pode se inscrever gratuitamente. Suas credenciais de conta são necessárias para criar uma conexão entre seu aplicativo lógico e sua conta Dropbox.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Neste exemplo, você precisa de um aplicativo lógico em branco.

## <a name="add-trigger"></a>Adicionar gatilho

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Na caixa de pesquisa, escolha **Tudo**. Na caixa de pesquisa, insira "dropbox" como o seu filtro.
Na lista de gatilhos, selecione este gatilho: **Quando um arquivo é criado**

   ![Selecionar o gatilho do Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Entre com suas credenciais de conta do Dropbox e autorize o acesso aos dados do Dropbox para os Aplicativos Lógicos do Azure.

1. Forneça as informações necessárias para o gatilho. 

   Neste exemplo, selecione a pasta onde deseja acompanhar a criação de arquivos. Para navegar em suas pastas, escolha o ícone da pasta ao lado da caixa **Pasta.**

## <a name="add-action"></a>Adicionar ação

Agora adicione uma ação que obtém o conteúdo de qualquer novo arquivo.

1. No gatilho, escolha **Próxima etapa**. 

1. Na caixa de pesquisa, escolha **Tudo**. Na caixa de pesquisa, insira "dropbox" como o seu filtro.
Na lista de ações, selecione esta ação: **Obtenha conteúdo de arquivo usando o caminho**

1. Se você ainda não autorizou o Azure Logic Apps a acessar o Dropbox, autorize o acesso agora.

1. Para navegar pelo caminho do arquivo que deseja usar, ao lado da caixa **Caminho de arquivo,** escolha o botão elipses (**...**). 

   Você também pode clicar dentro da caixa **Caminho de arquivos** e, a partir da lista de conteúdo dinâmico, selecione Caminho de **arquivo**, cujo valor está disponível como saída do gatilho adicionado na seção anterior.

1. Quando terminar, salve o aplicativo lógico.

1. Para ativar seu aplicativo lógico, crie um novo arquivo no Dropbox.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo de Swagger do conector, confira a [página de referência do conector](/connectors/dropbox/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
