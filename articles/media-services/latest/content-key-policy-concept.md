---
title: Políticas de Chave de Conteúdo nos Serviços de Mídia – Azure | Microsoft Docs
description: Este artigo fornece uma explicação de quais são as Políticas de Chave de Conteúdo e como elas são usadas pelos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7ddef1e78b4f8f62145e10b4cabc4537e28aba2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74969897"
---
# <a name="content-key-policies"></a>Políticas da Chave de Conteúdo

Com os Serviços de Mídia, é possível entregar o conteúdo ao vivo e sob demanda criptografado dinamicamente com a criptografia AES (AES-128) ou qualquer um dos três principais sistemas DRM (Gerenciamento de Direitos Digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados. 

Para especificar opções de criptografia em seu fluxo, você precisa criar uma [Política de Streaming](streaming-policy-concept.md) e associá-la ao seu [Localizador de Streaming](streaming-locators-concept.md). Você cria a [Política de Chave de Conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies) para configurar como a chave de conteúdo (que fornece acesso seguro aos seus [Ativos)](assets-concept.md)é entregue aos clientes finais. Você precisa definir os requisitos (restrições) na Política de Chave de Conteúdo que devem ser atendidas para que as chaves com a configuração especificada sejam entregues aos clientes. A política-chave de conteúdo não é necessária para streaming ou download claros. 

Normalmente, você associa sua política de chave de conteúdo com seu [localizador de streaming](streaming-locators-concept.md). Alternativamente, você pode especificar a política de chave de conteúdo dentro de uma [Política de Streaming](streaming-policy-concept.md) (ao criar uma política de streaming personalizada para cenários avançados). 

## <a name="best-practices-and-considerations"></a>Melhores práticas e considerações

> [!IMPORTANT]
> Por favor, revise as seguintes recomendações.

* Você deve projetar um conjunto limitado de políticas para sua conta do Serviço de Mídia e reutilizá-las para seus localizadores de streaming sempre que as mesmas opções forem necessárias. Para saber mais, consulte [Cotas e limitações](limits-quotas-constraints.md).
* As políticas-chave de conteúdo são atualizadas. Pode levar até 15 minutos para que os principais caches de entrega atualizem e peguem a política atualizada. 

   Ao atualizar a diretiva, você está substituindo seu cache de CDN existente, o que pode causar problema de reprodução para clientes que estão usando conteúdo em cache.  
* Recomendamos que você não crie uma nova política de chave de conteúdo para cada ativo. Os principais benefícios de compartilhar a mesma política de chave de conteúdo entre ativos que precisam das mesmas opções de política são:
   
   * É mais fácil gerenciar um pequeno número de políticas.
   * Se você precisar fazer atualizações para a política de chave de conteúdo, as alterações entrarão em vigor em todas as novas solicitações de licença quase imediatamente.
* Se você precisa criar uma nova política, você tem que criar um novo localizador de streaming para o ativo.
* Recomenda-se permitir que os Serviços de Mídia gerem automaticamente a chave de conteúdo. 

   Normalmente, você usaria uma chave de longa duração e verificaria a existência da política de chave de conteúdo com [get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get). Para ter acesso à chave, você precisa chamar um método de ação separado para obter segredos ou as credenciais. Confira o exemplo a seguir.

## <a name="example"></a>Exemplo

Para chegar à chave, use `GetPolicyPropertiesWithSecretsAsync`, como mostrado no Obter uma chave de assinatura a partir do exemplo de política [existente.](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

Confira [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md).

## <a name="additional-notes"></a>Observações adicionais

* As propriedades das Políticas-chave `Datetime` de conteúdo que são do tipo estão sempre no formato UTC.
* O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps"></a>Próximas etapas

* [Usar criptografia dinâmica AES-128 e o serviço de entrega de chave](protect-with-aes128.md)
* [Usar o serviço de entrega de licença e criptografia dinâmica do DRM](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
