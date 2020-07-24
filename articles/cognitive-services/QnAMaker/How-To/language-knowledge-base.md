---
title: Conceitos de linguagem-QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker dá suporte a conteúdo da base de dados de conhecimento em vários idiomas. No entanto, cada serviço de QnA Maker deve ser reservado para um único idioma. A primeira base de dados de conhecimento criada, direcionando um determinado serviço de QnA Maker, define o idioma desse serviço.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: b0d4250a6659996187923905955a9825a44cea42
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132612"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Suporte a idioma do conteúdo da base de dados de conhecimento para QnA Maker

Idioma do serviço é selecionado quando você cria a primeira base de dados de conhecimento no recurso. Todas as bases de dados de conhecimento adicionais no recurso devem estar no mesmo idioma.

O idioma determina a relevância dos resultados QnA Maker fornece em resposta a consultas de usuário.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Um idioma para todas as bases de dados de conhecimento no recurso

O QnA Maker permite que você selecione o idioma do serviço QnA ao criar a primeira base de dados de conhecimento. Todas as bases de dados de conhecimento em um recurso QnA Maker devem estar no mesmo idioma. Esse idioma não pode ser alterado.

A criação de bases de dados de conhecimento em diferentes idiomas em um recurso afeta negativamente a relevância dos resultados QnA Maker fornece em resposta a consultas de usuário.

Examine uma lista de [idiomas com suporte](../overview/language-support.md#languages-supported) e como as linguagens afetam a [correspondência e a relevância](#query-matching-and-relevance).

## <a name="select-language-when-creating-first-knowledge-base"></a>Selecione o idioma ao criar a primeira base de dados de conhecimento

A seleção de idioma faz parte das etapas para criar a primeira base de dados de conhecimento em um recurso.

![Captura de tela do portal do QnA Maker de seleção da linguagem para a primeira base de conhecimento](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Correspondência de consulta e relevância
QnA Maker depende dos [analisadores de linguagem do Azure pesquisa cognitiva](https://docs.microsoft.com/rest/api/searchservice/language-support) para fornecer resultados.

Embora os recursos de Pesquisa Cognitiva do Azure estejam em par para os idiomas com suporte, QnA Maker tem um classificador adicional que fica acima dos resultados do Azure Search. Neste modelo de classificação, usamos alguns recursos semânticos e baseados em palavras especiais nos idiomas a seguir.

|Idiomas com classificação adicional|
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

Essa classificação adicional é um trabalho interno do classificador de QnA Maker.

## <a name="verify-language"></a>Verificar idioma

Você pode verificar o idioma do QnA Maker recurso na página Configurações de serviço no QnA Maker.

![Captura de tela do portal do QnA Maker da página de configurações de serviço](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Migrar uma base de dados de conhecimento](../Tutorials/migrate-knowledge-base.md)
