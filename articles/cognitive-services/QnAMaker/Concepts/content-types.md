---
title: Tipos de conteúdo - QnA Maker
description: Os tipos de conteúdo incluem muitos documentos estruturados padrão, como PDF, DOC e TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 7c78f9ea261fa636cce50b69524802d0900e9d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650190"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Tipos de conteúdo de documentos que você pode adicionar a uma base de conhecimento
Os tipos de conteúdo incluem muitos documentos estruturados padrão, como PDF, DOC e TXT.

## <a name="file-and-url-data-types"></a>Tipos de dados de arquivos e URL

A tabela a seguir resume os tipos de conteúdo e formatos de arquivo com suporte no QnA Maker.

|Tipo de Fonte|Tipo de conteúdo| Exemplos|
|--|--|--|
|URL|Perguntas frequentes<br> (simples, com seções ou com uma página inicial de tópicos)<br>Páginas de suporte <br> (artigos de instrução de uma página, artigos de solução de problemas etc.)|[Perguntas frequentes simples](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Perguntas frequentes com links](https://www.microsoft.com/en-us/software-download/faq),<br> [Perguntas frequentes com home page de tópicos](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artigo de suporte](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|Perguntas frequentes,<br> Manual do Produto,<br> Folhetos,<br> Papel,<br> Política de folheto,<br> Guia de suporte,<br> QnA estruturado,<br> etc.|**Sem Multi-turn**<br>[QnA.doc estruturado](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Exemplo produto Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Exemplo semi-estruturado.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Amostra de papel branco.pdf,](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)<br><br>**Multi-volta:**<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso Benefícios (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso Benefícios (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Excel|Arquivo QnA estruturado<br> (incluindo suporte RTF, HTML)|[Exemplo de QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|*TXT/TSV|Arquivo QnA estruturado|[Exemplo de chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Se você precisar de autenticação para sua fonte de dados, considere os seguintes métodos para obter esse conteúdo no QnA Maker:

* Baixe o arquivo manualmente e importe no QnA Maker
* Adicionar arquivo a partir do local do [Sharepoint](../how-to/add-sharepoint-datasources.md) autenticado

## <a name="url-content"></a>Conteúdo de URL

Dois tipos de documentos podem ser importados via **URL** no QnA Maker:

* URLs de perguntas frequentes
* Suporte a URLs

Cada tipo indica um formato esperado.

## <a name="file-based-content"></a>Conteúdo baseado em arquivos

Você pode adicionar arquivos a uma base de conhecimento de uma fonte pública, ou seu sistema de arquivos local, no [portal QnA Maker](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Diretrizes de formato de conteúdo

Saiba mais sobre as [diretrizes](../reference-document-format-guidelines.md) de formato para os diferentes arquivos.

## <a name="next-steps"></a>Próximas etapas

Entenda quais informações são armazenadas em um [conjunto de perguntas e respostas (QnA).](question-answer-set.md)