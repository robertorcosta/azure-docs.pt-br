---
title: Migrar dos Serviços de Mídia do Azure v2 para v3 | Microsoft Docs
description: Este artigo descreve as alterações introduzidas nos Serviços de Mídia do Azure v3 e mostra as diferenças entre as duas versões. O artigo também fornece um guia para a migração dos Serviços de Mídia v2 para v3.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: serviços de mídia do azure, transmissão, difusão, ao vivo, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 72d413c5d8bc982d885d889da35b29a3607410cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472060"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Orientação de migração para passar dos Serviços de Mídia v2 para v3

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando esta URL: `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` no leitor de feed RSS.

Este artigo fornece a orientação de migração de Media Services v2 para v3.

E se você tiver um serviço de vídeo desenvolvido hoje em dia sobre as [APIs herdadas do Media Services v2](../previous/media-services-overview.md), analise as seguintes diretrizes e considerações antes de migrar para as APIs v3. Há muitos benefícios e novos recursos na API v3 que melhoram a experiência do desenvolvedor e os recursos dos Serviços de Mídia. No entanto, conforme explicado na seção [Problemas conhecidos](#known-issues) deste artigo, também há algumas limitações devido a alterações entre as versões da API. Esta página será mantida à medida que a equipe de Serviços de Mídia fizer melhorias contínuas nas APIs da v3 e abordar as lacunas entre as versões. 

## <a name="prerequisites"></a>Pré-requisitos

* Revisar [Serviços de Mídia v2 vs. v3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Benefícios do Serviços de Mídia do Microsoft Azure v3
  
### <a name="api-is-more-approachable"></a>A API é mais acessível

*  A v3 é baseada em uma superfície de API unificada que expõe a funcionalidade de operações e gerenciamento compilada no Azure Resource Manager. Os modelos do Azure Resource Manager podem ser usados para criar e implantar Transformações, Pontos de Extremidade de Streaming, Eventos ao Vivo, entre outros.
* [Documento de especificação OpenAPI (anteriormente chamado de Swagger).](https://aka.ms/ams-v3-rest-sdk)
    Expõe o esquema para todos os componentes de serviço, incluindo a codificação baseada em arquivo.
* SDKs disponíveis para [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref), e Ruby.
* [CLI do Azure](https://aka.ms/ams-v3-cli-ref) integração com o suporte a scripts simple.

### <a name="new-features"></a>Novos recursos

* Para processamento de trabalho baseado em arquivo, você pode usar um URL HTTP (S) como entrada.<br/>Você não precisa ter conteúdo já armazenado no Azure, nem precisa criar Ativos.
* Introduz o conceito de [Transformações](transforms-jobs-concept.md) para processamento de trabalho baseado em arquivo. Uma transformação pode ser usada para criar configurações reutilizáveis, criar modelos do Azure Resource Manager e isolar configurações de processamento entre vários clientes ou locatários.
* Um Ativo pode ter vários [Localizadores de Streaming](streaming-locators-concept.md), cada um com diferentes configurações de [Empacotamento Dinâmico](dynamic-packaging-overview.md) e Criptografia Dinâmica.
* [A proteção](content-key-policy-concept.md) de conteúdo oferece suporte a recursos multi-chave.
* Você pode transmitir Eventos ao Vivo com duração de até 24 horas ao usar os Serviços de Mídia para transcodificação de um feed de contribuição de taxa de bits única em um fluxo de saída que tem várias taxas de bits.
* Novo suporte de transmissão ao vivo de baixa latência em Eventos ao Vivo. Para obter mais informações, consulte [latência](live-event-latency.md).
* A Versão Prévia do Evento ao Vivo oferece suporte ao [Empacotamento Dinâmico](dynamic-packaging-overview.md) e à Criptografia Dinâmica. Isso permite proteção de conteúdo na Versão Prévia, bem como empacotamento HLS e DASH.
* A Saída Dinâmica é mais simples de usar do que a entidade Program nas APIs v2. 
* Suporte aprimorado do RTMP (maior estabilidade e mais suporte de codificador de código-fonte).
* Ingestão segura de RTMPS.<br/>Ao criar um Evento ao Vivo, você obtém 4 URLs de ingestão. As 4 URLs de ingestão são quase idênticas, têm o mesmo token de streaming (AppId) e apenas a parte do número da porta é diferente. Duas das URLs são primárias e de backup para RTMPS.   
* Você tem controle de acesso baseado em função (RBAC) sobre suas entidades. 

## <a name="known-issues"></a>Problemas conhecidos

*  Atualmente, você pode usar o [portal Azure](https://portal.azure.com/) para:

    * gerenciar Serviços de Mídia v3 [Eventos ao Vivo,](live-events-outputs-concept.md) 
    * exibir (não gerenciar) [ativos](assets-concept.md)v3, 
    * [obter informações sobre como acessar APIs](access-api-portal.md). 

    Para todas as [outras tarefas](transforms-jobs-concept.md) de gerenciamento (por exemplo, Transformações e Empregos e [Proteção de Conteúdo),](content-protection-overview.md)use a [API REST,](https://docs.microsoft.com/rest/api/media/) [CLI](https://aka.ms/ams-v3-cli-ref)ou uma das [SDKs](media-services-apis-overview.md#sdks)suportadas.
* Você precisa para provisionar unidades reservadas de mídia (MRUs) em sua conta para controlar a simultaneidade e o desempenho de seus trabalhos, especialmente os que envolvem a análise de áudio ou de vídeo. Para obter mais informações, consulte [Scaling Media Processing](../previous/media-services-scale-media-processing-overview.md) (Colocação em escala do processamento de mídia). Você pode gerenciar as MRUs usando [cli 2.0 para serviços de mídia v3,](media-reserved-units-cli-how-to.md)usando o [portal Azure,](../previous/media-services-portal-scale-media-processing.md)ou usando as [APIs v2](../previous/media-services-dotnet-encoding-units.md). Você precisa provisionar MRUs, se você estiver usando os Serviços de Mídia do Microsoft Azure v2 ou v3 APIs.
* As entidades do Serviços de Mídia do Microsoft Azure criadas com a API v3 não podem ser gerenciadas pela API v2.  
* Nem todas as entidades da API V2 aparecem automaticamente na API V3.  A seguir estão exemplos de entidades nas duas versões que são incompatíveis:  
    * Trabalhos e tarefas criados na v2 não aparecem na v3, pois não estão associados a uma transformação. A recomendação é mudar para v3 Transforms e Jobs. Haverá um período de tempo relativamente curto de necessidade de monitorar as tarefas V2 durante a transição.
    * Os Canais e os Programas criados com a v2 (que são mapeados para os Eventos ao Vivo e as Saídas Dinâmicas na v3) não podem continuar sendo gerenciados com a v3. A recomendação é alternar para os Eventos ao Vivo e as Saídas Dinâmicas da v3 em uma parada de Canal conveniente.<br/>Atualmente, você não pode migrar continuamente executando canais.  

> [!NOTE]
> Esta página será mantida à medida que a equipe de Serviços de Mídia fizer melhorias contínuas nas APIs da v3 e abordar as lacunas entre as versões.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

[Tutorial: Encodifique um arquivo remoto baseado em URL e transmita o vídeo - .NET](stream-files-dotnet-quickstart.md)
