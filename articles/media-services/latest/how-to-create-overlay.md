---
title: Como criar uma sobreposição com Media Encoder Standard
description: Saiba como criar uma sobreposição com Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 6c93408bce8da9f8cd0e4a0d0bab615e2bd362dc
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267319"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Como criar uma sobreposição com Media Encoder Standard

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O Codificador de Mídia Padrão permite sobrepor uma imagem em um vídeo existente. Atualmente, há suporte para os seguintes formatos: png, jpg, gif e bmp.

## <a name="prerequisites"></a>Pré-requisitos

* Colete as informações da conta de que você precisa para configurar o *appsettings.jsno* arquivo no exemplo. Se você não tiver certeza de como fazer isso, consulte [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft](../../active-directory/develop/quickstart-register-app.md). Os valores a seguir são esperados no *appsettings.jsno* arquivo.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Region": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Se você ainda não estiver familiarizado com as transformações, é recomendável que você conclua as seguintes atividades:

* Ler [codificação de vídeo e áudio com os serviços de mídia](encoding-concept.md)
* Leia [como codificar com uma transformação personalizada-.net](customize-encoder-presets-how-to.md). Siga as etapas neste artigo para configurar o .NET necessário para trabalhar com transformações e, em seguida, retornar aqui para experimentar uma amostra predefinida de sobreposições.
* Consulte o [documento de referência de transformações](/rest/api/media/transforms).

Quando estiver familiarizado com as transformações, baixe o exemplo de sobreposições.

## <a name="overlays-preset-sample"></a>Amostra predefinida de sobreposições

Baixe a [amostra Media-Services-Overlay](https://github.com/Azure-Samples/media-services-overlays) para começar com sobreposições.

## <a name="next-steps"></a>Próximas etapas

* [Subclipe um vídeo ao codificar com os serviços de mídia-.NET](subclip-video-dotnet-howto.md)