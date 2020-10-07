---
title: Design para QnA Maker de idiomas
description: O recurso QnA Maker e todas as bases de dados de conhecimento dentro desse recurso dão suporte a uma única linguagem. A linguagem única é necessária para fornecer os melhores resultados de resposta para uma consulta.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: e09e15f71b0574a5612e7f9bacd8aaa4739a441c
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776996"
---
# <a name="design-knowledge-base-for-content-language"></a>Design da base de dados de conhecimento para linguagem de conteúdo

O recurso QnA Maker e todas as bases de dados de conhecimento dentro desse recurso oferecem suporte a um único idioma. A linguagem única é necessária para fornecer os melhores resultados de resposta para uma consulta.

## <a name="single-language-per-resource"></a>Idioma único por recurso

QnA Maker considerações para suporte a idiomas:

* Um serviço QnA Maker e todas as suas bases de dados de conhecimento oferecem suporte apenas a um idioma.
* O idioma é definido explicitamente quando a primeira base de dados de conhecimento do serviço é criada
* O idioma é determinado dos arquivos e URLs adicionados quando a base de dados de conhecimento é criada
* O idioma não pode ser alterado para nenhuma outra base de dados de conhecimento no serviço
* O idioma é usado pelo serviço de Pesquisa Cognitiva (#1 de classificação) e o serviço de QnA Maker (#2 de classificação) para gerar a melhor resposta a uma consulta

## <a name="supporting-multiple-languages"></a>Suporte a vários idiomas

Se você precisar dar suporte a um sistema de base de dados de conhecimento, que inclui várias linguagens, você pode escolher um dos seguintes métodos:

* Use o [serviço de texto de tradução](../../translator/translator-info-overview.md) para converter uma pergunta em um único idioma antes de enviar a pergunta para sua base de dados de conhecimento. Isso permite que você se concentre na qualidade de uma única linguagem e na qualidade das perguntas e respostas alternativas.
* Crie um recurso QnA Maker e uma base de dados de conhecimento dentro desse recurso, para cada idioma. Isso permite que você gerencie perguntas alternativas separadas e texto de resposta que é mais nuance para cada idioma. Isso proporciona muito mais flexibilidade, mas requer um custo de manutenção muito maior quando as perguntas ou as respostas são alteradas em todos os idiomas.

Examine os [idiomas com suporte](../overview/language-support.md) para QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Suporte a cada idioma com um recurso de QnA Maker

* Criar um recurso de QnA Maker para cada idioma
* Adicionar somente arquivos e URLs para esse idioma
* Use uma Convenção de nomenclatura para o recurso para identificar o idioma. Um exemplo é `qna-maker-fr` para todas as bases de dados de conhecimento para documentos em francês

## <a name="next-steps"></a>Próximas etapas

Aprenda [conceitos](query-knowledge-base.md) sobre como consultar a base de dados de conhecimento para obter uma resposta.