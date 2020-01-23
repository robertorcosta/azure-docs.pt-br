---
title: Obter uma chave de assinatura de uma política usando os serviços de mídia do Azure v3 .NET
description: Este tópico mostra como obter uma chave de assinatura da política existente usando o SDK do .NET do Serviços de Mídia v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: ff27ae0fd639316f03fe89ffc906561b3ef85f6f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76515064"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Obter uma chave de assinatura da política existente

Um dos princípios de design de chave da API v3 é tornar a API mais segura. as APIs v3 não retornam segredos ou credenciais em operações de **obtenção** ou de **lista** . Consulte a explicação detalhada aqui: para obter mais informações, consulte [RBAC e contas de serviços de mídia](rbac-overview.md)

O exemplo neste artigo mostra como usar o .NET para obter uma chave de assinatura da política existente. 
 
## <a name="download"></a>Baixar 

Clone um repositório do GitHub que contém o exemplo do .NET Core completo em seu computador usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
O ContentKeyPolicy com exemplo de segredos está localizado na pasta [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM).

## <a name="get-contentkeypolicy-with-secrets"></a>Obter ContentKeyPolicy com segredos 

Para obter a chave, use **GetPolicyPropertiesWithSecretsAsync**, conforme mostrado no exemplo a seguir.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Próximos passos

[Criação de um sistema de proteção de conteúdo de DRM múltiplo com controle de acesso](design-multi-drm-system-with-access-control.md) 
