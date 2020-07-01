---
title: O que é o Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Saiba como usar Content Moderator para acompanhar, sinalizar, avaliar e filtrar material inadequado em conteúdo gerado pelo usuário.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: f28f2bcf5d04c9a6354b8135bd39546b9d8b9bf3
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81404291"
---
# <a name="what-is-azure-content-moderator"></a>O que é o Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O Azure Content Moderator é um serviço cognitivo que verifica conteúdo de textos, imagens e vídeos para verificar material que possa ser ofensivo, de risco ou, de alguma maneira, indesejável. Quando tal material é encontrado, o serviço aplica rótulos (sinalizadores) apropriados ao conteúdo. Seu aplicativo pode tratar de conteúdo sinalizado para cumprir as normas ou manter o ambiente desejado para os usuários. Confira a seção [APIs de Moderação](#moderation-apis) para saber mais sobre o que os vários sinalizadores de conteúdo indicam.

## <a name="where-its-used"></a>Como ele é usado

Estes são alguns cenários nos quais um desenvolvedor ou equipe de software usaria o Content Moderator:

- Marketplaces online que moderam catálogos de produtos e outros tipos de conteúdo gerados pelo usuário.
- Empresas de jogos que moderam artefatos de jogos gerados pelo usuário e salas de chat.
- Plataformas de mensagens sociais que moderam imagens, textos e vídeos adicionados pelos usuários.
- Empresas de mídia corporativa que implementam moderação de conteúdo centralizada para seu conteúdo.
- Provedores de soluções de ensino fundamental e médio que filtram conteúdo considerado inapropriado para alunos e professores.

> [!NOTE]
> Não é possível usar o Content Moderator para examinar imagens ilícitas de exploração infantil. No entanto, as organizações qualificadas podem usar o [Serviço de Nuvem PhotoDNA](https://www.microsoft.com/photodna "Serviço de nuvem Microsoft PhotoDNA") para examinar este tipo de conteúdo.

## <a name="what-it-includes"></a>O que inclui

O serviço Content Moderator consiste em várias APIs de serviço Web disponíveis por meio de chamadas REST e um SDK do .NET. Ele também inclui a ferramenta de análise, o que permite que revisores humanos ajudem o serviço e aprimorem ou ajustem sua função de moderação.

## <a name="moderation-apis"></a>APIs de Moderação

O serviço Content Moderator inclui APIs de Moderação que verificam se o conteúdo do material que é potencialmente indesejável ou inapropriado.

![diagrama de bloco para APIs de moderação do Content Moderator](images/content-moderator-mod-api.png)

A tabela a seguir descreve os diferentes tipos de APIs de moderação.

| Grupo de APIs | Descrição |
| ------ | ----------- |
|[**Moderação de texto**](text-moderation-api.md)| Examina o texto quanto a conteúdo ofensivo, conteúdo sexualmente explícito ou sugestivo, conteúdo ofensivo e dados pessoais.|
|[**Listas de termos personalizadas**](try-terms-list-api.md)| Examina o texto em relação a uma lista de termos personalizados juntamente com os termos internos. Use listas personalizadas para bloquear ou permitir conteúdo de acordo com suas próprias políticas de conteúdo.|  
|[**Moderação de imagem**](image-moderation-api.md)| Verifica conteúdo adulto ou erótico em imagens, detecta textos em imagens com o recurso de OCR (reconhecimento óptico de caracteres) e detecta rostos.|
|[**Listas de imagens personalizadas**](try-image-list-api.md)| Verifica imagens em relação a uma lista personalizada de imagens. Use listas de imagem personalizadas para filtrar instâncias de conteúdo recorrentes que você não deseja classificar novamente.|
|[**Moderação de vídeo**](video-moderation-api.md)| Verifica conteúdo adulto ou erótico em vídeos e retorna os marcadores de tempo para esse conteúdo.|

## <a name="review-apis"></a>Analisar APIs

As APIs de Revisão permitem integrar seu pipeline de moderação com os revisores humanos. Use as operações [Trabalhos](review-api.md#jobs), [Revisões](review-api.md#reviews) e [Fluxo de Trabalho](review-api.md#workflows) para criar e automatizar fluxos de trabalho com envolvimento humano (human-in-the-loop) dentro da [Ferramenta de Revisão](#review-tool) (abaixo).

> [!NOTE]
> A API de Fluxo de Trabalho ainda não está disponível no SDK do .NET, mas pode ser usada com o ponto de extremidade REST.

![diagrama de bloco para APIs de revisão do Content Moderator](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Ferramenta de análise

O serviço do Content Moderator também inclui a [ferramenta Examinar](Review-Tool-User-Guide/human-in-the-loop.md) baseada na Web, que hospeda o conteúdo de revisões para moderadores humanos processarem. A entrada humana não treina o serviço, mas o trabalho combinado do serviço e das equipes de análise humana permite que os desenvolvedores alcancem o equilíbrio entre a precisão e a eficiência. A ferramenta de Análise também fornece um front-end amigável para uma variedade de recursos do Content Moderator.

![Page inicial da ferramenta de análise do Content Moderator](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço Content Moderator devem estar cientes das políticas da Microsoft em relação aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Comece a usar o serviço Content Moderator seguindo as instruções descritas em [Experimentar o Content Moderator na Web](quick-start.md).
