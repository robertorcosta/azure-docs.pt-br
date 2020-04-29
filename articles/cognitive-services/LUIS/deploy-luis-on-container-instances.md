---
title: Implantar o contêiner LUIS em instâncias de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Implante o contêiner LUIS em uma instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: 08af17106846a0f5f7a0ccc2b01da1b2e15c1143
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80879185"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Implantar o contêiner Reconhecimento vocal (LUIS) nas instâncias de contêiner do Azure

Saiba como implantar o contêiner [Luis](luis-container-howto.md) de serviços cognitivas nas [instâncias de contêiner](https://docs.microsoft.com/azure/container-instances/)do Azure. Este procedimento demonstra a criação de um recurso de detector de anomalias. Em seguida, discutimos a extração da imagem de contêiner associada. Por fim, destacamos a capacidade de exercitar a orquestração dos dois em um navegador. O uso de contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure

O contêiner LUIS requer um `.gz` arquivo de modelo que é extraído no tempo de execução. O contêiner deve ser capaz de acessar esse arquivo de modelo por meio de uma montagem de volume da instância de contêiner. Para obter informações sobre como criar um compartilhamento de arquivos do Azure, consulte [criar um compartilhamento de arquivos](../../storage/files/storage-how-to-create-file-share.md). Anote o nome da conta de armazenamento do Azure, a chave e o nome do compartilhamento de arquivos, pois você precisará deles mais tarde.

### <a name="export-and-upload-packaged-luis-app"></a>Exportar e carregar o aplicativo LUIS empacotado

Para carregar o modelo LUIS (aplicativo empacotado) no compartilhamento de arquivos do Azure, você precisará <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">exportá-lo do portal do Luis <span class="docon docon-navigate-external x-hidden-focus"> </span>primeiro </a>. No portal do Azure, navegue até a página **visão geral** do recurso de conta de armazenamento e selecione **compartilhamentos de arquivos**. Selecione o nome do compartilhamento de arquivos que você criou recentemente e, em seguida, selecione o botão **carregar** .

> [!div class="mx-imgBorder"]
> ![Carregar no compartilhamento de arquivos](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Carregue o arquivo de modelo LUIS.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
