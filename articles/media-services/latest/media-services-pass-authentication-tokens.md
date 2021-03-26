---
title: Passar tokens de autenticação para os serviços de mídia v3 | Microsoft Docs
description: Saiba como enviar tokens de autenticação do cliente para o serviço de distribuição de chaves dos serviços de mídia v3
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 590309ad392876ba3c5cb6d21abe777ab5a93efb
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572337"
---
# <a name="pass-tokens-to-the-azure-media-services-v3-key-delivery-service"></a>Passar tokens para o serviço de entrega de chaves dos serviços de mídia do Azure v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Os clientes frequentemente perguntam como um player pode passar tokens para o serviço de entrega de chaves dos Serviços de Mídia do Azure para verificação para que o player possa obter a chave. Os Serviços de Mídia são compatíveis com formatos SWT (Token Web Simples) e JWT (Token Web JSON). A autenticação de token é aplicada a qualquer tipo de chave, independentemente de você usar uma criptografia comum ou criptografia de envelope AES no sistema.

 Dependendo do player e plataforma intencionados, você pode passar o token com o seu player dos seguintes modos:

## <a name="pass-a-token-through-the-http-authorization-header"></a>Passar um token por meio do cabeçalho de autorização HTTP

> [!NOTE]
> O prefixo "Portador" é esperado para as especificações de OAuth 2.0. Para definir a fonte de vídeo, escolha **AES (Token JWT)** ou **AES (Token SWT)**. O token é passado por meio do cabeçalho de autorização.

## <a name="pass-a-token-via-the-addition-of-a-url-query-parameter-with-tokentokenvalue"></a>Passe um token por meio da adição de um parâmetro de consulta de URL com "token = TokenValue".

> [!NOTE]
> O prefixo "Portador" não é esperado. Como o token é enviado por uma URL, é necessário proteger a cadeia de caracteres do token. Observe aqui um código de exemplo C# sobre como fazer isso:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

## <a name="pass-a-token-through-the-customdata-field"></a>Passar um token por meio do campo CustomData

Essa opção é usada apenas para a aquisição de licença do PlayReady, pelo campo CustomData do Desafio de Aquisição de Licença do PlayReady. Nesse caso, o token deve estar dentro do documento XML descrito aqui:

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```

Coloque o seu token de autenticação no elemento Token.
