---
title: Conectar-se ao Dropbox
description: Carregar e gerenciar arquivos com as APIs REST do Dropbox e os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: b38f9b9626e5eec0272256aacc71f7503a006dd6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789824"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Carregar e gerenciar arquivos no Dropbox usando o aplicativo lógico do Azure

Com o conector do Dropbox e os aplicativos lógicos do Azure, você pode criar fluxos de trabalho automatizados que carregam e gerenciam arquivos em sua conta do dropbox. 

Este artigo mostra como se conectar ao dropbox de seu aplicativo lógico e, em seguida, adicionar o gatilho **quando um arquivo é criado** e o **conteúdo do Dropbox Get file usando** a ação Path.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma [conta do Dropbox](https://www.dropbox.com/), que você pode assinar gratuitamente. Suas credenciais de conta são necessárias para criar uma conexão entre seu aplicativo lógico e sua conta do dropbox.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Neste exemplo, você precisa de um aplicativo lógico em branco.

## <a name="add-trigger"></a>Adicionar gatilho

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Na caixa de pesquisa, escolha **Tudo**. Na caixa de pesquisa, insira "dropbox" como o seu filtro.
Na lista de gatilhos, selecione este gatilho: **Quando um arquivo é criado**

   ![Selecionar o gatilho do Dropbox](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Entre com suas credenciais de conta do Dropbox e autorize o acesso aos dados do Dropbox para os Aplicativos Lógicos do Azure.

1. Forneça as informações necessárias para o gatilho. 

   Neste exemplo, selecione a pasta onde você deseja acompanhar a criação do arquivo. Para procurar suas pastas, escolha o ícone de pasta ao lado da caixa **pasta** .

## <a name="add-action"></a>Adicionar ação

Agora, adicione uma ação que obtém o conteúdo de qualquer arquivo novo.

1. No gatilho, escolha **Próxima etapa**. 

1. Na caixa de pesquisa, escolha **Tudo**. Na caixa de pesquisa, insira "dropbox" como o seu filtro.
Na lista ações, selecione esta ação: **obter o conteúdo do arquivo usando o caminho**

1. Se você ainda não tiver autorizado o aplicativo lógico do Azure para acessar o Dropbox, autorize o acesso agora.

1. Para procurar o caminho do arquivo que você deseja usar, ao lado da caixa **caminho do arquivo** , escolha o botão de reticências ( **...** ). 

   Você também pode clicar dentro da caixa **caminho do arquivo** e, na lista conteúdo dinâmico, selecionar **caminho do arquivo**, cujo valor está disponível como saída do gatilho adicionado na seção anterior.

1. Quando terminar, salve o aplicativo lógico.

1. Para disparar seu aplicativo lógico, crie um novo arquivo no dropbox.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo OpenAPI (anteriormente Swagger) do conector, consulte a [página de referência do conector](/connectors/dropbox/).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
