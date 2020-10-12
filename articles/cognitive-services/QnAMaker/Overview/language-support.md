---
title: Suporte de idioma – QnA Maker
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas naturais e cultura com suporte do QnA Maker para sua base de dados de conhecimento. Não misture idiomas na mesma base de dados de conhecimento.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/24/2019
ms.openlocfilehash: c990b6980dea871679b0b301e293e4fb94748db7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89650900"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Suporte a idiomas para um recurso QnA Maker e bases de dados de conhecimento

Idioma do serviço é selecionado quando você cria a primeira base de dados de conhecimento no recurso. Todas as bases de dados de conhecimento adicionais no recurso devem estar no mesmo idioma. 

O idioma determina a relevância dos resultados QnA Maker fornece em resposta a consultas de usuário. O recurso QnA Maker e todas as bases de dados de conhecimento dentro desse recurso oferecem suporte a um único idioma. A linguagem única é necessária para fornecer os melhores resultados de resposta para uma consulta.

## <a name="single-language-per-resource"></a>Idioma único por recurso

Considere o seguinte:

* Um serviço QnA Maker e todas as suas bases de dados de conhecimento oferecem suporte apenas a um idioma.
* O idioma é definido explicitamente quando a primeira base de dados de conhecimento do serviço é criada
* O idioma é determinado dos arquivos e URLs adicionados quando a base de dados de conhecimento é criada
* O idioma não pode ser alterado para nenhuma outra base de dados de conhecimento no serviço
* O idioma é usado pelo serviço de Pesquisa Cognitiva (#1 de classificação) e o serviço de QnA Maker (#2 de classificação) para gerar a melhor resposta a uma consulta

## <a name="supporting-multiple-languages"></a>Suporte a vários idiomas

Se você precisar dar suporte a um sistema de base de dados de conhecimento, que inclui várias linguagens, você pode:

* Use o [serviço de Tradutor](../../translator/translator-info-overview.md) para converter uma pergunta em um único idioma antes de enviar a pergunta para sua base de dados de conhecimento. Isso permite que você se concentre na qualidade de uma única linguagem e na qualidade das perguntas e respostas alternativas.
* Crie um recurso QnA Maker e uma base de dados de conhecimento dentro desse recurso, para cada idioma. Isso permite que você gerencie perguntas alternativas separadas e texto de resposta que é mais nuance para cada idioma. Isso proporciona muito mais flexibilidade, mas requer um custo de manutenção muito maior quando as perguntas ou as respostas são alteradas em todos os idiomas.

Examine os [idiomas com suporte](../overview/language-support.md) para QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Suporte a cada idioma com um recurso de QnA Maker

* Criar um recurso de QnA Maker para cada idioma
* Adicionar somente arquivos e URLs para esse idioma
* Use uma Convenção de nomenclatura para o recurso para identificar o idioma. Um exemplo é `qna-maker-fr` para todas as bases de dados de conhecimento para documentos em francês


## <a name="languages-supported"></a>Idiomas compatíveis

A lista a seguir contém os idiomas com suporte para um recurso de QnA Maker. 

|Linguagem|
|--|
|Árabe|
|Armênia|
|Bangla|
|Basco|
|Búlgaro|
|Catalão|
|Chinês_Simplificado|
|Chinês_Tradicional|
|Croata|
|Tcheco|
|Dinamarquês|
|Holandês|
|Inglês|
|Estoniano|
|Finlandês|
|Francês|
|Galego|
|Alemão|
|Grego|
|Guzerate|
|Hebraico|
|Híndi|
|Húngaro|
|Islandês|
|Indonésio|
|Irlandês|
|Italiano|
|Japonês|
|canarim|
|Coreano|
|Letão|
|Lituano|
|Malaiala|
|Malaio|
|Norueguês|
|Polonês|
|Português|
|Panjabi|
|Romeno|
|Russo|
|Cirílico sérvio|
|sérvio Latino|
|Eslovaco|
|Esloveno|
|Espanhol|
|Sueco|
|Tâmil|
|Télugo|
|Tailandês|
|Turco|
|Ucraniano|
|Urdu|
|Vietnamita|

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

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Seleção de idioma](../how-to/language-knowledge-base.md)
