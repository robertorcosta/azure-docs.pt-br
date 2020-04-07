---
title: Implantar contêiner LUIS em instâncias de contêiner Azure
titleSuffix: Azure Cognitive Services
description: Implante o contêiner LUIS em uma instância do Azure Container e teste-o em um navegador da Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dapine
ms.openlocfilehash: f1a0a08351a03e46d6c3a1e82b68ecea6e36c015
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757252"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Implantar o contêiner Decompreensão de Idiomas (LUIS) em instâncias de contêiner do Azure

Saiba como implantar o contêiner [LUIS](luis-container-howto.md) de Serviços Cognitivos nas [instâncias do Contêiner](https://docs.microsoft.com/azure/container-instances/)Azure . Este procedimento demonstra a criação de um recurso do Detector de Anomalias. Em seguida, discutimos puxar a imagem do contêiner associado. Finalmente, destacamos a capacidade de exercitar a orquestração dos dois a partir de um navegador. O uso de contêineres pode desviar a atenção dos desenvolvedores do gerenciamento da infra-estrutura para, em vez disso, focar no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure

O contêiner LUIS `.gz` requer um arquivo modelo que é puxado em tempo de execução. O contêiner deve ser capaz de acessar este arquivo modelo através de uma montagem de volume a partir da instância Container. Para obter informações sobre a criação de um compartilhamento de arquivos Do Zure, consulte [criar um compartilhamento de arquivos](../../storage/files/storage-how-to-create-file-share.md). Tome nota do nome da conta do Azure Storage, chave e nome de compartilhamento de arquivos, pois você precisará deles mais tarde.

### <a name="export-and-upload-packaged-luis-app"></a>Exportar e enviar aplicativo LUIS embalado

Para enviar o modelo LUIS (aplicativo embalado) para o compartilhamento de arquivos do Azure, você precisará <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">exportá-lo do portal LUIS primeiro <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. A partir do portal Azure, navegue até a página **Visão Geral** do recurso da conta de armazenamento e selecione **Compartilhamentos de arquivos**. Selecione o nome de compartilhamento de arquivos que você criou recentemente e selecione o botão **Upload.**

> [!div class="mx-imgBorder"]
> ![Faça upload para compartilhar arquivos](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Faça upload do arquivo modelo LUIS.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
