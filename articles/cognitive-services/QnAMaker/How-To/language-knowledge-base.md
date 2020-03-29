---
title: Conceitos de linguagem - QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker dá suporte a conteúdo da base de dados de conhecimento em vários idiomas. No entanto, cada serviço de QnA Maker deve ser reservado para um único idioma. A primeira base de conhecimento criada, visando um determinado serviço QnA Maker, define a linguagem desse serviço.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 38701e8bbef1c5d78eca2242105e81fe7261c0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220628"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Suporte a idioma do conteúdo da base de dados de conhecimento para QnA Maker

O idioma para o serviço é selecionado quando você cria a primeira base de conhecimento no recurso. Todas as bases de conhecimento adicionais no recurso devem estar na mesma língua.

O idioma determina a relevância dos resultados que o QnA Maker fornece em resposta às consultas do usuário.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Uma linguagem para todas as bases de conhecimento em recursos

O QnA Maker permite que você selecione o idioma para o seu serviço de QnA, enquanto cria a primeira base de conhecimento. Para todas as bases de conhecimento em um recurso QnA Maker, todos eles devem estar na mesma língua. Esta linguagem não pode ser mudada.

A criação de bases de conhecimento em diferentes idiomas em um recurso afeta negativamente a relevância dos resultados que o QnA Maker fornece em resposta às consultas do usuário.

Revise uma lista de [idiomas suportados](../overview/language-support.md#languages-supported) e como as línguas impactam [a correspondência e a relevância](#query-matching-and-relevance).

## <a name="select-language-when-creating-first-knowledge-base"></a>Selecione o idioma ao criar a primeira base de conhecimento

A seleção de idiomas faz parte das etapas para criar a primeira base de conhecimento em um recurso.

![Captura de tela do portal QnA Maker de seleção de linguagem para primeira base de conhecimento](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Correspondência de consulta e relevância
O QnA Maker depende [dos analisadores de linguagem de pesquisa cognitiva do Azure](https://docs.microsoft.com/rest/api/searchservice/language-support) para fornecer resultados.

Enquanto os recursos de Pesquisa Cognitiva do Azure estão no mesmo nível para idiomas suportados, o QnA Maker tem um ranker adicional que está acima dos resultados de pesquisa do Azure. Neste modelo de ranker, usamos algumas características semânticas e baseadas em palavras nas seguintes línguas.

|Idiomas com ranker adicional|
|--|
|Chinês|
|Tcheco|
|Holandês|
|Inglês|
|Francês|
|Alemão|
|Húngaro|
|Italiano|
|Japonês|
|Coreano|
|Polonês|
|Português|
|Espanhol|
|Sueco|

Este ranking adicional é um trabalho interno do ranker do Fabricante QnA.

## <a name="verify-language"></a>Verificar o idioma

Você pode verificar o idioma do recurso QnA Maker a partir da página de configurações de serviço no QnA Maker.

![Captura de tela do portal QnA Maker da página de configurações do serviço](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Migrar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)
